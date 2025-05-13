---
title: How to Run Google Lighthouse in WSL2
categories : [Tutorial]
tags :  [wsl2,lighthouse,javascript,linux]
description: Learn how to run Google Lighthouse inside WSL2 on Windows, including setup steps and Chrome installation.
comments: true
---

Lighthouse is a performance auditing tool for websites. If you try to use the Lighthouse library (version 12.6.0 or before) with the Node CLI or programmatically on WSL2, you will observe a similar error:

```
Failed to launch Chrome: Error: connect ECONNREFUSED 127.0.0.1:34729
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1636:16) {
  errno: -111,
  code: 'ECONNREFUSED',
  syscall: 'connect',
  address: '127.0.0.1',
  port: 34729
}
```

Unfortunately, the official Lighthouse documentation does not work out of the box on WSL2. The goal of this post is to provide clear instructions for running Lighthouse successfully in a WSL2 environment.

## Using Lighthouse in the CLI

### Installation

Check your node version and **ensure that you have Node 18.20 or later**:

```bash
node --version
```

If you have an older version of Node, you can install a new version using the [`nvm`](https://github.com/nvm-sh/nvm) tool.

Install Google Chrome on WSL2. On Ubuntu, the commands are (Spence, 2021):

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt -y install ./google-chrome-stable_current_amd64.deb
rm google-chrome-stable_current_amd64.deb
```

If the installation was successful, you should be able to launch Chrome with the `google-chrome` command in the terminal. You can also check your chrome version using `google-chrome --version`.

Finally, install the Lighthouse package globally:

```bash
npm install -g lighthouse
```

If the installation is successful, you should be able to run `lighthouse --version`.

### Usage

To use Lighthouse, you first need to launch Chrome in headless mode with remote debugging:

```bash
google-chrome --headless --remote-debugging-port=9222 --user-data-dir=remote-profile
```

You might see an error message similar to that shown below but you can safely ignore it. This is not a fatal error and is not important for Lighthouse to work.

```
Failed to connect to the bus: Failed to connect to socket /var/run/dbus/system_bus_socket: No such file or directory
```



Finally, open **another terminal** to run the following command:

```bash
lighthouse https://creme332.github.io/ --output json --port=9222   
```

> Do not forget to specify the port in the command.
{: .prompt-warning }

Once you are done with Lighthouse, you can kill the first terminal which was responsible for launching Chrome.

## Using Lighthouse programmatically

### Installation

Add Lighthouse and puppeteer as dependencies to your project:

```bash
npm install lighthouse puppeteer
```
We are using Puppeteer because the [official Lighthouse documentation](https://github.com/GoogleChrome/lighthouse/tree/main/docs#using-programmatically) relies on `chrome-launcher` to launch Chrome programmatically, but this consistently results in an `ECONNREFUSED` error on WSL2 for unknown reasons.

You do not need to install Chrome in this case. Puppeteer installs and uses its own version of Chrome known as Chrome For Testing (Bynens, 2023).

### Usage

Here is a minimal example that shows how to run Lighthouse:

```js
import lighthouse from "lighthouse";
import puppeteer from "puppeteer";

async function start(url) {
  try {
    const browser = await puppeteer.launch({
      args: ["--remote-debugging-port=9222"], // Needed for Lighthouse
    });

    const options = {
      port: 9222, // Tell Lighthouse to use the existing Chrome
    };

    const runnerResult = await lighthouse(url, options);
    const score = runnerResult.lhr.categories.performance.score * 100;
    console.log("Performance score = ", score);

    await browser.close();
  } catch (err) {
    console.error(err);
  }
}

await start("https://creme332.github.io/");
```

If you want to use your own Chrome browser instead the Chrome For Testing one, you need to determine the path to your Chrome executable using `which google-chrome` and modify the puppeteer script as follows:

```diff
const browser = await puppeteer.launch({
+    executablePath: "/usr/bin/google-chrome", // add your path here
    args: ["--remote-debugging-port=9222"],
});
```

## References

1. thasmo, 2021. How to use lighthouse CLI via WSL2? [online]. Github. Available at: [https://github.com/GoogleChrome/lighthouse/discussions/12752](https://github.com/GoogleChrome/lighthouse/discussions/12752) [Accessed 13 May 2025].
2. Spence, S., 2021. Use Google Chrome in Ubuntu on Windows Subsystem Linux [online]. Available at: [https://www.scottspence.com/posts/use-chrome-in-ubuntu-wsl](https://www.scottspence.com/posts/use-chrome-in-ubuntu-wsl) [Accessed 13 May 2025].
3. Google Chrome, 2025. lighthouse [online]. Github. Available at: [https://github.com/GoogleChrome/lighthouse](https://github.com/GoogleChrome/lighthouse) [Accessed 13 May 2025].
4. Bynens, M., 2023. Chrome for Testing: reliable downloads for browser automation [online]. Available at: [https://developer.chrome.com/blog/chrome-for-testing](https://developer.chrome.com/blog/chrome-for-testing) [Accessed 13 May 2025].