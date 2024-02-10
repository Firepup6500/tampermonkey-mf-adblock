---
layout: post
title: "mf-adblock 0.7: A Tamper Monkey Adblocker"
published: true
tags: [adblock, tampermonkey, browser]
---
You can find the original gist [here](https://gist.github.com/DerFichtl/af462414df838ff397603f8bf9db24e3), which most of this description was pulled from.

## mf-adblock: A Tamper Monkey Adblocker

This ad-blocker script for Tampermonkey won't trigger an adblock-block/adblock-detector script and so you could use it for pages that are annoying you with "please turn off your adblocker" messages. It just hides the ads and doesn't try to intercept the requests or remove the ads at all. So it won't make the websites faster, but it makes them cleaner and easier to read.

This script is not a generic solution and has to be configured for every website it should clean up. I don't provide configs for pages, just use your browser developer-tools to find the elements you want remove or click.

### What is Tampermonkey?

Tampermonkey is a browser extension for Firefox and Google Chrome that allows you to run scripts on websites. You can find Tampermonkey in the extension repository of your favorite browser vendor. Tampermonkey is a very popular plugin with hundreds of thousands downloads.

* Chrome: https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo
* Firefox: https://addons.mozilla.org/firefox/addon/tampermonkey/

### Ad-block Config Options

#### `click`
An array of css-selectors to click when the script is running. It's used to click away the cookie-consent banners or other popups.
```javascript
{
    'click': ['.cookies','#decline-cookies'],
}
```
#### `remove`
An array of css-selectors of elements to remove when the script is running. Make sure that you have the right selector. Ads usually make iframes around them, so you have to find the element that consists the ad's iframe.
```javascript
{
    'remove': ['.ads','#powerup-ad'],
}
```
#### `interaction`
A Boolean to trigger a fake mouse-move at document load. You can use that for sites that wait for user-interaction to show consent-boxes or ads.
```javascript
{
    'interaction': true, // or false
}
```
#### `timeout`
A timeout in milliseconds to wait after document loads to start the ad-block script.
```javascript
{
    'timeout': 1000, // 1 second
}
```
#### `interval`
A interval in milliseconds to repeat the ad-block script after. Used to remove ads that are loaded on scroll or a timeout after the document is ready. Don't use numbers that are very small, a few seconds (3000ms to 5000ms) between the runs usually works the best.
```javascript
{
    'interval': 5000, // 5 seconds
}
```
#### `background`
Sets a background color for sites that are doing full page custom ads with changed background color. It also sets `overflow:scroll` and `position:static` which is sometimes needed if you removed a blocking modal.
```javascript
{
    'background': '#ffffff', // white
}
```
#### `onChange`
A Boolean on whether or not to re-fire the script if the page changes (hash change, push state, etc.)
```javascript
{
    onChange: true, // Redo everything once page changes, but no refresh/reload occurs (IE: Discourse)
}
```
#### `antiVignette`
A Boolean on whether or not to auto fix issues cause by google vignette.
```javascript
{
    antiVignette: true, // Auto fix google vignette based issues
}
```
#### `trueRemove`
A Boolean on whether or not to actually remove elements instead of hiding them (WARNING: Might trigger adblock detection!)
```javascript
{
    trueRemove: true // Actually remove elements instead of hiding them (WARNING: Might trigger adblock detection!)
}
```
### Tampermonkey Script Code
```javascript
// ==UserScript==
// @name         mf-adblock
// @namespace    none
// @version      0.7
// @description  A basic anti-adblock workaround that can remove or click elements on a website
// @author       DerFichtl, with major improvements by Firepup650
// @match        https://*/*
// @icon         https://getadblock.com/favicon.ico
// @grant        none
// @noframes
// ==/UserScript==

(function() {
    'use strict';
    (() => {
        let oldPushState = history.pushState;
        history.pushState = function pushState() {
            let ret = oldPushState.apply(this, arguments);
            window.dispatchEvent(new Event('pushstate'));
            window.dispatchEvent(new Event('locationchange'));
            return ret;
        };

        let oldReplaceState = history.replaceState;
        history.replaceState = function replaceState() {
            let ret = oldReplaceState.apply(this, arguments);
            window.dispatchEvent(new Event('replacestate'));
            window.dispatchEvent(new Event('locationchange'));
            return ret;
        };

        window.addEventListener('popstate', () => {
            window.dispatchEvent(new Event('locationchange'));
        });
    })();
    let sites = {
        /*
        'example.com': { // website domain, can be regex.
            remove: ['.some-class','#some-element'], // Hide these elements (used for ads) - (DOES NOT REMOVE THEM, JUST HIDES THEM)
            click: ['.class','#element'], // Click these elements (used for cookie consent)
            interaction: true, // Move mouse cursor to trigger `onmousemove` ads
            timeout: 2000, // In MS, how long to wait before doing anything
            interval: 0, // In MS, how long to wait before redoing everything (Ignored if 0 or missing) (used if ads are added onscroll or timeout)
            onChange: true, // Redo everything once page changes, but no refresh/reload occurs (IE: Discourse)
            antiVignette: true, // Auto fix google vignette based issues
            trueRemove: true // Actually remove elements instead of hiding them (WARNING: Might trigger adblock detection!)
            background: '#ffffff' // Set a background-color, overflow:scroll, and position (used for custom fullpage ads)
        },
        */
        '.*\.?fandom\.com': {
            remove: ['.top-ads-container','.bottom-ads-container','#WikiaBar','.notifications-placeholder','.gpt-ad'],
            interaction: true,
            timeout: 2000,
            trueRemove: true
        },
    }

    let interval = null;

    let hostname = document.location.hostname;

    let loaded = false;

    function cleanup() {
        console.log("Running cleanup...");
        if(sites[hostname].interaction) {
            document.body.dispatchEvent(new MouseEvent('mousemove'));
        }

        if(sites[hostname].remove) {
            let selectors = sites[hostname].remove;

            selectors.forEach(function(selector) {
                let elements = document.querySelectorAll(selector);

                console.log(selector, elements);

                elements.forEach(function(elem) {
                    if (!sites[hostname].trueRemove) {
                        elem.style.visibility = 'hidden';
                        elem.style.width = '1px';
                        elem.style.height = '1px';
                        elem.style.overflow = 'hidden';
                        elem.style.opacity = 0;
                    } else {
                        elem.remove();
                    }
                });
            });
        }

        if(sites[hostname].background) {
            document.body.style.background = sites[hostname].background;
            document.body.style.overflow = 'scroll';
            document.body.style.position = 'static';
        }

        if(sites[hostname].click) {
            let selectors = sites[hostname].click;

            selectors.forEach(function(selector) {
                let element = document.querySelector(selector);

                if(!!element) {
                    element.click();
                }
            });
        }

        if(sites[hostname].antiVignette && window.location.hash && window.location.hash == "#google_vignette") {
            window.location.href = window.location.href.split("#")[0]
        }
    }

    if (!Object.keys(sites).indexOf(hostname) >= 0) {
        for (const site in sites) {
            const regex = new RegExp(site, "i");
            if(regex.test(hostname)) {
                hostname = site;
            }
        }
    }

    if(Object.keys(sites).indexOf(hostname) >= 0) {

        let timeout = 0;
        if(sites[hostname].timeout) {
            timeout = sites[hostname].timeout;
        }

        window.setTimeout(function(){
            cleanup();
        }, timeout);

        if(sites[hostname].interval && !interval) {
            interval = window.setInterval(function(){
                cleanup();
            }, sites[hostname].interval);
        }

        if(sites[hostname].onChange && !loaded) {
            loaded = true;
            window.addEventListener('locationchange', function () {
                window.setTimeout(function(){
                    cleanup();
                }, timeout);
            });
        }
    }
})();
```
