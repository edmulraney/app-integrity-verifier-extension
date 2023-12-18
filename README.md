# End-to-end app integrity verification using browser extension manifest v3

## The problem
In Manifest v2, we were able to easily intecept and read server responses via the webRequestBlocking API. Manifest v2 is deprecated, and the webRequestBlocking API was replaced with DNR (declarativeNetRequest) in Manifest v3. DNR doesn't allow you to read responses or manipulate them. Instead, we need to use other APIs.

# Intro
We have a client side app (./server/index.html) which is distributed via a web server.

This extension verifies that the server only distributes authentic versions of them i.e. not tampered with.

# Extension
The extension performs the following steps:

1. Block the request to the app URL via DNR (declarativeNetRequest API)
2. Intercept top level browser navigation to the server, using the tabs API
3. Register a response interceptor using the debugger API
4. Hash the response and check it matches the expected hash
5. If they match, allow the response to pass through to the user
6. If they don't match, redirect to an error page

This process ensures that when a user visits the app, their browser will only load it if it passes the integrity checks.

# Server
The server distributes a client side app: ./server/index.html.

# Testing
1. Navigate to chrome://extensions
2. Toggle "Developer mode" in the top right corner if you haven't already
3. Click "Load unpacked" button in the top left corner
4. Select the "extension" directory of this repository
5. The extension will now be loaded, you can turn it on or off with the toggle
6. Run `npm start` from this repository to start the http://localhost:9191 server 
7. Visit http://localhost:9191 in your browser to see the extension verify the integrity
8. Modify ./server/index.html and reload http://localhost:9191 in your browser to see the extension block the request and redirect to an error page

# Firefox equivalent
This same extension can be built much easier in Firefox using the [filterResponseData API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/webRequest/filterResponseData).

# Gotchas
There appears to be a bug in Chrome whereby if you have devtools open on the network tab, the chrome.tabs.update API just hangs forever when called from the debugger interceptor. I recommend testing the extension with devtools closed.

# Todo
1. Check if browser caching affects interception if the user has already visited the site without the extension and has a local cached version