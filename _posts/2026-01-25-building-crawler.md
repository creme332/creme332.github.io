---
title: "Building a Website Downloader"
categories: [Case Study]
tags: [web scraping, crawler, puppeteer, javascript, spa, web archiving]
description: A case study on designing and implementing a custom website downloader.
comments: true
---

A few months ago, I completed my final-year project for my bachelor’s degree in Computer Science. One of the milestones of my project was to build a website downloader (crawler) for downloading any website and self-hosting the downloaded content on a server. This local version of the website then undergoes optimization and testing later in the project. 

While there are existing tools for mirroring websites, none of them fully met the requirements of my project, particularly when dealing with modern JavaScript-heavy websites. In this post, I reflect on the design choices behind my website downloader and the issues and limitations I encountered during its development.

## Functional Requirements

The functional requirements of my crawler were as follows:

1. All dependencies (images, CSS, JS, etc.) of the website should be downloaded.
2. The website source code and assets (HTML, JS, CSS, images, etc.) must be preserved in their original, editable format so that they can be modified programmatically.
3. The downloaded website should be storable at any directory path in the filesystem without breaking links. The link structure of the original website must be modified accordingly to keep everything relative.
4. It should be capable of recursively downloading web pages.
5. A user should be able to browse through the "mirrored" website page by page; i.e., links between pages should be relative.
6. It should work on Single Page Applications (SPAs), which are JS-heavy websites such as React applications that use client-side rendering and routing.
7. It should work on server-side rendered websites. The downloaded version should function as a static website.
8. It should circumvent anti-bot policies (to some extent).
9. The downloaded website should be visually similar to the original website.
10. The mirrored website should not contain trackers or ads.

## Related Work

Before I began working on my own website downloader, I explored the available tools to see if any matched all my needs.

### Basic CLI Utilities

`curl` and `wget` are two command-line tools that can be used to mirror a website locally. For example, the following command can be used to mirror the `https://lexpress.mu` website:

```bash
wget \
  --mirror \
  --convert-links \
  --adjust-extension \
  --page-requisites \
  --no-parent \
  --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" \
  --header="Referer: https://example.com/" \
  --header="Accept-Language: en-US,en;q=0.9" \
  https://lexpress.mu
```

However, both `wget` and `curl` have two major drawbacks:

1. They are easy to detect and block by websites implementing anti-bot policies.
2. They do not work on Single Page Applications or client-side rendered websites because they cannot execute JavaScript.

### Website Archiving Tools

There also exist production-ready tools for archiving websites. Examples include [HTTrack](https://www.httrack.com/), [ArchiveBox](https://github.com/ArchiveBox/ArchiveBox), [Heritrix3](https://github.com/internetarchive/heritrix3), and [Zeno](https://github.com/internetarchive/Zeno), among others. These tools are used by archiving services such as the Internet Archive Wayback Machine.

I avoided these tools because:

1. They store websites in formats (e.g., WARC) that make programmatic editing difficult. The archived versions are not designed to be modified.
2. They are written in languages (Go, Elixir, Java) that are not web-oriented. I wanted to use JavaScript or TypeScript because the NPM ecosystem is geared toward web development and contains libraries for DOM manipulation.
3. They are too complex for my use case. If I encountered an issue (for example, if a tool failed on a particular website), it would be difficult to find a solution because the codebases are large and complex. Writing my own tool made it easier to modify the source code and integrate new changes.

### MHTML

Saving a webpage in MHTML format was a technique I heavily considered due to its ease of use. It downloads a webpage into a single file and embeds all dependencies (images, CSS, etc.) into that file. It works on many websites, and the code required was straightforward:

```js
const puppeteer = require('puppeteer');
const fs = require('fs');

(async function main() {
  try {
    const browser = await puppeteer.launch();
    const [page] = await browser.pages();

    await page.goto('https://en.wikipedia.org/wiki/MHTML');

    const cdp = await page.target().createCDPSession();
    const { data } = await cdp.send('Page.captureSnapshot', { format: 'mhtml' });
    fs.writeFileSync('page.mhtml', data);

    await browser.close();
  } catch (err) {
    console.error(err);
  }
})();
```

However, I ultimately decided against using this format because I needed to modify the assets for optimization purposes. The encoding used by MHTML makes this difficult.

## Choice of Tools

### Browser Automation

I wanted to create my own crawler from scratch, so I decided to use the [Puppeteer](https://pptr.dev/) library for browser automation. All crawling logic was written manually. This gave me more flexibility in how files are downloaded and stored.

### DOM Manipulation

To parse HTML and edit parts of it, I used the [Cheerio](https://www.npmjs.com/package/cheerio) library. It is more robust than approaches involving regex or string searching.

### Sending Requests

For sending requests, I used [`ky`](https://www.npmjs.com/package/ky) instead of the native `fetch` function because `ky` comes with built-in retry logic and has configurable timeouts.

For concurrency control, I used the [`p-limit`](https://www.npmjs.com/package/p-limit) library.

## Implementation

### Skeleton

To create the first version of my crawler, I followed a scraping tutorial from [oida.dev](https://oida.dev/scraping-with-puppeteer/). The code was easy to read and understand. The following piece of code uses Puppeteer to save a page as HTML and download all its assets:

```js
async function start(urlToFetch) {
  /* create browser context */
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  /* record responses and create a callback for each response */
  page.on('response', async (response) => {
    const url = new URL(response.url());
    let filePath = path.resolve(`./output${url.pathname}`);
    if (path.extname(url.pathname).trim() === '') {
      filePath = `${filePath}/index.html`;
    }
    await fse.outputFile(filePath, await response.buffer());
  });

  /* wait for network requests to stabilize */
  await page.goto(urlToFetch, {
    waitUntil: 'networkidle2'
  });

  await browser.close();
}

start('https://oida.dev');
```

This code is a great starting point because it downloads all resources separately and the static HTML file is viewable on a web server. Unfortunately, it suffers from the following issues:

1. It has no way of bypassing anti-bot policies.
2. The file path of the downloaded assets is not sanitized.
3. We might download the same resource multiple times due to the lack of URL deduplication. Moreover, with the current setup, we are unable to perform downloads concurrently.
4. We do not clearly know when to close the browser or stop recording responses. A response may still be in flight, and if it is not handled, the asset will not be downloaded.
5. It does not perform URL rewrites in the HTML code, resulting in broken links for CSS and other assets on some websites.

### Bypassing Anti-bot Policies

To deal with anti-bot protection, I used [Puppeteer Extra](https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra), a drop-in replacement for Puppeteer, together with the [`StealthPlugin`](https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth). This plugin uses several evasion techniques to hide the fact that a headless browser is being used, making it harder for servers to block requests. One such technique is modifying the User-Agent to better mimic a real browser. For a complete list of evasions, refer to the plugin’s [GitHub repository](https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth/evasions).

```js
import puppeteer from "puppeteer-extra";
import StealthPlugin from "puppeteer-extra-plugin-stealth";

puppeteer.use(StealthPlugin());
```

### Generating Safe Filenames

When saving an asset, we need a way to map the asset URL to a filesystem path. The strategy I went with favors a clean, flat directory tree (`example_com/index.html`, `example_com/about/index.html`, …). It also handles long file names and query parameters with hashing. 

```js
/**
 * Generates a unique file name for an asset. It is important to hash the asset URL as it may contain query parameters.
 * @param {URL} assetUrl Download link of asset. It can contain query parameters.
 * @param {string | undefined} ext File extension of asset. If undefined, it will be automatically determined from URL but is not guaranteed to be correct.
 * @returns Path to asset relative to index.html of webpage
 */
export function getAssetFileName(
  assetUrl: URL,
  ext: string | undefined = undefined
) {
  const fileExtension = (
    ext ??
    (path.extname(assetUrl.pathname) || ".bin")
  ).toLowerCase();

  // Group assets by file extensions
  let subfolder = fileExtension.slice(1); // remove leading dot
  const imageExtensions = new Set([".png", ".jpg", ".jpeg"]);
  const fontExtensions = new Set([".woff", ".woff2", ".ttf", ".otf"]);

  if (imageExtensions.has(fileExtension)) {
    subfolder = "img";
  } else if (fontExtensions.has(fileExtension)) {
    subfolder = "font";
  }

  const queryHash = hashNormalizedQuery(assetUrl.searchParams);

  const hashInput = `${assetUrl.origin}${assetUrl.pathname}?${queryHash}`;
  const hash = crypto
    .createHash("sha1")
    .update(hashInput)
    .digest("hex")
    .slice(0, 10);

  return path.join("assets", subfolder, `asset-${hash}${fileExtension}`);
}
```

### Detecting SPAs

For websites that use JavaScript to render content, we do not want the crawler to retrieve the fully rendered version, as this can lead to duplicate content issues. Instead, we want to retrieve the original HTML containing template placeholders or SPA containers so that, when the user accesses the site locally, the JavaScript runs once on a clean HTML document.

To achieve this, we need to detect whether a website is an SPA and disable JavaScript when accessing such websites.

However, we do not want to disable JavaScript on all websites, because we may miss content in cases where JavaScript is responsible for loading only a subset of assets.

{% raw %}
```js
// Evaluate the page to detect signs of a JavaScript-heavy single-page app (SPA).
// Heuristics:
// - Common SPA containers (#root, #app)
// - Very little visible or structural content
// - Presence of unresolved template placeholders (e.g., {{someVariable}})
const isLikelyJSApp = await page.evaluate(() => {
  const bodyText = document.body.innerText.trim();
  const bodyHTML = document.body.innerHTML.trim();

  const hasRoot = !!document.querySelector("#root, #app");
  const veryEmpty = bodyText.length < 100 || bodyHTML.length < 500;
  const placeholderMatches = bodyHTML.match(/{{[^{}]+}}/g);
  const placeholderCount = placeholderMatches
    ? placeholderMatches.length
    : 0;

  return placeholderCount >= 5 || (hasRoot && veryEmpty);
});
```
{% endraw %}

### Rewriting URLs

To ensure that all URLs are relative (rather than absolute or root-relative), I first defined an array of HTML elements with URL-bearing attributes:

```jsx
const assetElements = [
  ["img", "src"],
  ["script", "src"],
  ["input[type=image]", "src"],
  ["link[rel=stylesheet]", "href"],
  ["link[rel=preload]", "href"],
  ["link[rel=apple-touch-icon]", "href"],
  ["link[rel=icon]", "href"],
  ["link[rel=manifest]", "href"],
  ["link[rel=mask-icon]", "href"],
  ["source", "src"],
  ["audio", "src"]
];
```

Then, I parsed the HTML using Cheerio, rewriting the URLs by first resolving them relative to the page URL.

Sample cases for assets in `https://www.example.com/index.html`:

| Original URL                   | Rewritten URL         |
| ------------------------------ | --------------------- |
| `cat.png`                      | `assets/png/cat.png`  |
| `/cat.png`                     | `assets/png/cat.png`  |
| `//cat.png`                    | `assets/png/cat.png`  |
| `https://www.abc.com/ping.jpg` | `assets/jpg/ping.jpg` |

> Complete table
{prompt-warning}

```js
// Process each asset URL in the HTML tags
const $ = cheerio.load(html);

for (const [selector, attr] of assetElements) {
  $(selector).each((_, el) => {
    const originalUrl = $(el).attr(attr);

    // ignore data URI
    if (originalUrl.startsWith("data:image")) return;

    const normalizedURL = new URL(originalUrl, pageURL); // absolute URL

    if (normalizedURL.href === pageURL.href) return;

    if (selector === "link[rel=stylesheet]") {
      // Get path where asset will be saved and force the extension to .css.
      // This avoids cases where assets have .php or .aspx extensions.
      const assetPath = getAssetFileName(normalizedURL, ".css");
      assetUrls.set(normalizedURL.href, assetPath);
    } else if (selector === "script") {
      // Ensure the asset filename has a .js extension.
      assetUrls.set(
        normalizedURL.href,
        getAssetFileName(normalizedURL, ".js")
      );
    } else {
      // For other assets, deduce the extension
      assetUrls.set(normalizedURL.href, getAssetFileName(normalizedURL));
    }

    // Rewrite the asset link in HTML
    $(el).attr(attr, assetUrls.getValue(normalizedURL.href));
  });
}
```

A similar logic is applied to process links in CSS files.

### Concurrent Downloads with Automatic Retry

To perform concurrent downloads with a limit of 10, I used the [`p-limit`](https://www.npmjs.com/package/p-limit) library. Limiting concurrent requests, as opposed to using `Promise.all` directly, is important to avoid overwhelming the servers handling the requests. To retry downloads in case of failure, I used the [`ky`](https://www.npmjs.com/package/ky) library inside my `downloadWithRetry` function.

```js
async function downloadAssets(
  assetUrls: ReadonlyMap<string, string>,
  localDir: string
): Promise<void> {
  const downloadLimit = pLimit(10); // Limit concurrency

  const validDownloadTasks: Array<Promise<void>> = [];

  for (const [assetUrl, assetStoragePath] of assetUrls.entries()) {
    if (!assetStoragePath) {
      continue;
    }

    const absoluteStoragePath = path.join(localDir, assetStoragePath);

    // Pre-check if file already exists to skip unnecessary async task creation
    const fileExists = await fse.pathExists(absoluteStoragePath);
    if (fileExists) continue;

    validDownloadTasks.push(
      downloadLimit(async () => {
        logger.info(`Additional download: ${assetUrl}`);
        await downloadWithRetry(assetUrl, absoluteStoragePath);
      })
    );
  }

  await Promise.all(validDownloadTasks);
}
```

### Removing Ads and Trackers

To remove trackers and ad scripts, I used a manual approach by identifying common analytics and advertising providers. This approach operates purely at the DOM level and does not address fetch-based tracking and beacon requests.

```js
// Clean the DOM by removing unwanted scripts, meta tags, and iframes
await page.evaluate(() => {
  // Remove Google site verification tag
  document
    .querySelectorAll('meta[name="google-site-verification"]')
    .forEach((el) => el.remove());

  // Remove CSP meta tag
  document
    .querySelectorAll('meta[http-equiv="Content-Security-Policy"]')
    .forEach((el) => el.remove());

  // Remove external scripts from known tracking/analytics providers
  const scriptBlacklist = [
    "recaptcha",
    "googletagmanager",
    "google-analytics",
    "gtag/js",
    "facebook.net",
    "hotjar",
    "segment",
    "matomo",
    "doubleclick"
  ];

  document
    .querySelectorAll<HTMLScriptElement>("script[src]")
    .forEach((script) => {
      if (scriptBlacklist.some((domain) => script.src.includes(domain))) {
        script.remove();
      }
    });

  // Remove inline scripts containing tracking logic
  document
    .querySelectorAll<HTMLScriptElement>("script:not([src])")
    .forEach((script) => {
      const content = script.textContent || "";
      if (/gtag|ga\(|fbq|_paq|dataLayer/.test(content)) {
        script.remove();
      }
    });

  // Remove iframes from known tracker sources
  const iframeBlacklist = ["recaptcha", "doubleclick", "facebook.com"];
  document
    .querySelectorAll<HTMLIFrameElement>("iframe")
    .forEach((iframe) => {
      const src = iframe.src || "";
      if (iframeBlacklist.some((domain) => src.includes(domain))) {
        iframe.remove();
      }
    });
});
```

## Limitations

- Global resources for the same website are duplicated across different folders.
- Websites that use service workers may not be faithfully mirrored.
- Content delivered via real-time channels such as  WebSockets are not captured.
- URLs constructed dynamically in JavaScript (e.g., string concatenation, runtime API calls) cannot be statically rewritten.
- Pages behind login walls or requiring session-specific cookies are not fully supported.
- There is no isolation between mirrored sites or pages beyond directory separation. Reference:
[https://github.com/ArchiveBox/ArchiveBox/issues/239](https://github.com/ArchiveBox/ArchiveBox/issues/239)
- Some websites (e.g., [react.dev](https://react.dev)) modifies my rewritten URLs back to their original values during rehydration.
