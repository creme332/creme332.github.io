---
title: Exploiting an IDOR Vulnerability in UOM's E-Library
categories : [Cybersecurity]
tags :  [idor,bash]
description: A step-by-step breakdown of how I discovered an IDOR vulnerability in UOM's E-Library.
comments: true
media_subpath: /assets/idor/
---

## Motivation

This whole adventure started because I wanted to download a dissertation for offline viewing but [UOM's e-library](https://library.uom.ac.mu/) provided no way of doing so. On top of that, the experience of viewing a dissertation on their website was frustrating due to the constant login requirements and clunky animations.

## Discovery

After logging into the e-library and accessing the full document of a random online dissertation, I decided to inspect the HTML source code to understand how the viewer worked.

The website uses FlipHTML5, a dynamic flip-book for displaying a dissertation. To achieve this, it imports some JavaScript libraries for FlipHTML5 and uses an `iframe` to contain the dissertation. An `iframe` is an HTML element that can load another HTML page. 

<img src="javascript-imports.png" height ="446" width="702" alt = "JavaScript imports for FlipHTML">
_JavaScript imports for FlipHTML_

<img src="iframe.png" height ="446" width="702" alt = "iframe HTML code in Developer Tools">
_HTML code of iframe_

The code that stood out to me was the URL inside the `#document` element. This link corresponds to the original HTML page for the dissertation. I pasted this link into an incognito tab and to my surprise I could access the dissertation **without any login**. This is the first **unprotected** URL that I found.

<img src="incognito.png" height ="446" width="702" alt = "FlipHTML5 dissertation in an incognito tab">
_FlipHTML5 dissertation in an incognito tab_

I then decided to take a look at the Networks tab of the Chrome Developer Tools. Each time the next page button is clicked, two new requests are made for the images to be displayed since 2 pages are displayed at once. The page names follow a numbered sequence: `1.jpg`, `2.jpg`, `3.jpg`, ... 

<img src="network-tab.png" height ="446" width="702" alt = "Network tab of Developer Tools showing image requests">
_Network tab of Developer Tools showing image requests_

By accessing the request URL of a particular request, I discovered the second **unprotected** URL:

<img src="detailed-request.png" height ="446" width="702" alt = "Detailed information about a particular image request">
_Detailed information about a particular image request_

The Request URL contains a timestamp as query parameter to track when a request was made. This can be safely ignored when making a request.

To summarize my findings:

1. The viewable document is not in PDF format. Each page is a JPEG image that is then displayed in the FlipHTML viewer. The pages are requested on demand.
2. The URL corresponding to the FlipHTML5 version of a dissertation is **unprotected**.
3. The pages are named `1.jpg`, `2.jpg`, `3.jpg`, ...
4. **The image URLs follow a specific format**. This means that it is possible to request any page once the image URL of one page is known.
5. **The image URLs are NOT protected**, i.e., they can be accessed online without any login. 

### URL Format

URL of the record page of a dissertation:

```
https://library.uom.ac.mu/elib/App_WebPages/ProfilePage.aspx?rsn=<RECORD_NO>
```

URL of a dissertation in FlipHTML5 format: 

```
https://library.uom.ac.mu/dissertations/<DEPT>/<DEGREE>/<YEAR>-<NAME>/index.html#p=<PAGE_NO>
```

URL of a page of a dissertation:

```
https://library.uom.ac.mu/dissertations/<DEPT>/<DEGREE>/<YEAR>-<NAME>/files/page/<PAGE_NO>.jpg
```

| Parameter   | Meaning                                                   | Possible values         |
| ----------- | --------------------------------------------------------- | ----------------------- |
| `RECORD_NO` | Record number of e-resource as defined by the library     | A unique 8-digit number |
| `DEPT`      | Department where dissertation was submitted               | FOICDT, FMHS, ...       |
| `DEGREE`    | Type of degree                                            | BSc, PhD, ...           |
| `YEAR`      | Year of dissertation submission                           | 2024, 2023, …           |
| `NAME`      | Full name of dissertation author in hyphenated title case | Smith-John              |
| `PAGE_NO`   | Page number (counting from 1)                             | 1, 2, 3, …              |


The next question that arises is how to determine these parameters from a record number but no login access.
We cannot use the `Click here for full document` option on a record page and extract the URL from the source code since we will be redirected to the login page. 
The solution is to use the export feature available for a dissertation to obtain its bibliographic details.

<img src="catalogue.png" height ="446" width="702" alt = "Catalogue information of a dissertation">

### MARC Format

**MARC** (**MAchine-Readable Cataloging**) is a standard set of digital for the machine-readable description of items catalogued by libraries, such as books, DVDs, and digital resources (Wikipedia Contributors, 2024). The e-library fortunately allows the **general public** to export bibliographic information of any dissertation in multiple formats including MARC format. 

There are three ways to download the MARC file of a dissertation both without login:

1. By making a GET request to the following endpoint, where `selected` is set to the record number and `token` is set to a random number:
    
    ```
    https://library.uom.ac.mu/libero/User.WebOpac.Catalogue.RecordDownload.cls?set=1&selected=<RECORD_NO>&what=this&format=marc&subjects=0&tags=&to=file&emailaddress=&token=<RANDOM_NUMBER>
    ```

    For example, to download the MARC file of the record `10239364`, the following Bash command can be executed:
    
    ```bash
    curl --output marc.txt https://library.uom.ac.mu/libero/User.WebOpac.Catalogue.RecordDownload.cls\?set\=1\&selected\=10239364\&what\=this\&format\=marc\&subjects\=0\&tags\=\&to\=file\&emailaddress\=\&token\=1
    ```
    
2. On the dissertation page, we can click on the `Download Title` button (located beside the `Reserve Title` button). This button click has the same effect as the first method.
3. On the results page, we can select a dissertation (by clicking on the checkbox next to it) and then clicking on the `Download All` button found at the bottom of the page.
    <img src="download-marc.gif" height ="446" width="702" alt = "MARC file for a dissertation">
    _Steps for downloading the MARC file of a dissertation_

<img src="marc.png" height ="446" width="702" alt = "MARC file for a dissertation">
_Contents of a sample MARC file for a dissertation_

Each entry has a meaning. For example the first entry with ID `001-1-00-$` represents the record number. We can map each entry to the parameter that we need:

| Parameter   | Entry ID for derivation |
| ----------- | ----------------------- |
| `RECORD_NO` | `001-1-00-$`            |
| `DEPT`      | `710-1-00-$a`           |
| `DEGREE`    | `710-2-00-$a`           |
| `YEAR`      | `260-1-00-$c`           |
| `NAME`      | `100-1-00-$a`           |

However, we can skip all this derivation and use last entry `997-1-00-$u`. This represents the absolute path to the dissertation on the library’s server. It contains all the required information to construct the unprotected URL formats!

## Proof of Concept

To confirm the existence of the vulnerability beyond doubt, I implemented a [bash script that automates the downloading of a dissertation given its record number](https://github.com/creme332/libero2pdf). The script does not login to the library and accesses it as a guest.

The pseudocode is as follows:

1. Input a record number.
2. Download the details of the record in MARC format.
3. Extract the relevant path information from the MARC file.
4. Using the result from Step 3, initialize the base URL for the website where the dissertation pages are located.
5. Initialize a page counter variable to 1.
6. Enter an infinite loop.
   1. Create a new URL by concatenating the base URL with the current page number.
   2. Download the page and save it as an image file.
   3. If the page was successfully downloaded, increment the page counter and restart loop.
   4. Else, the end of the dissertation is reached so exit the loop.
7.  Once all pages have been downloaded, convert the image files to a PDF file.
8.  Delete the temporary image and MARC files.

> **This approach can be adapted to download any e-resource (past paper, e-publications, ...) on the library.**
{: .prompt-danger }

### What Makes This a Vulnerability

The library user guide states that (UOM, 2018):

> As a member of the general public, you need to login & you shall be prompted to pay the subscription fee before being able to access the e-Resources.

**The vulnerability allows the general public to gain access to any e-resource without login or subscription fee**. This is known as an Insecure Direct Object Reference (IDOR) vulnerability and can be mitigated with access control checks (Cheat Sheets Series Team, 2025).

## Reporting & Mitigation Suggestions

I've emailed the university on two separate occasions regarding the vulnerability but received no response even though my email was forwarded to the Management Information System (MIS) team. Three months after my initial email, the only noticeable change was the removal of the `Download Title` button on the dissertation’s record page. This appeared to be a quick fix aimed at hiding the absolute server path to the dissertation. Unfortunately, this is insufficient because:

- Even without access to the MARC format, the parameters in the unprotected URLs can still be determined from the record page — it just takes more effort.
- The URL for downloading the bibliographic information in MARC format still works.
- The URLs for each page of a dissertation remain unprotected.

A better solution to this vulnerability is to check whether a request to the unprotected URLs has an associated session. Since a session is only created when a user logs in, if no session is found, redirect the user to the login page.

## References

1. Cheat Sheets Series Team, 2025. Insecure Direct Object Reference Prevention Cheat Sheet [online]. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html) [Accessed 13 April 2025].
2. Wikipedia Contributors, 2024. MARC standards. Wikipedia, The Free Encyclopedia. Available at: [https://en.wikipedia.org/w/index.php?title=MARC_standards&oldid=1214958905](https://en.wikipedia.org/w/index.php?title=MARC_standards&oldid=1214958905) [Accessed 13 April 2025].
3. UOM, 2018. User Guide [online]. University of Mauritius Library. Available at: [https://library.uom.ac.mu/elib/userGuide5.pdf](https://library.uom.ac.mu/elib/userGuide5.pdf) [Accessed 13 April 2025].