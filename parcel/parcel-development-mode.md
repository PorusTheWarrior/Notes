# Development Mode

## Dev server

> -p, --port : Override default port

> --host : Allow specific hosts only

> --open : Choose browser eg. --open safari

## Hot reloading

### Auto rebuild

Automaticallly rebuilds the changed files

By default it fully reloads the page

### Hot Module Replacement (HMR)

Only change updated part in module/page, no whole page refresh . eg. CSS changes

## Development Target

Transpilation of modern JavaScript syntax for older browsers

Only a single target can built in Dev mode.

> eg. --target legacy , --target default

## Lazy Mode

Build entire app -> dev server start : take lot of time

> parcel 'pages/\*.html' --lazy

Page will be built on demand

Defer building files until they are requested in the browser.

## Caching

Parcel caches everything it builds to disk

Only rebuilds files that have changed.

> "<b>.parcel-cache</b>" => All cache is stored in this file

Add this file to <b>.gitignore</b>

Disable caching using <b> --no-cache </b> flag. This will only disables reading from the cache folder , <b>.parcel-cache</b> still be created.

## HTTPS

Provide HTTPS during development

> <b>parcel src/index.html --https</b> => provide automatically generated self-signed cerificate

> <b>parcel src/index.html --cert certificate.cert --key private.key</b> => custom certificate

## API proxy

### .proxyrc / .proxyrc.json

```
{
  "/api": {
    "target": "http://localhost:8000/",
    "pathRewrite": {
      "^/api": ""
    }
  }
}
```

### .proxyrc.js

```
const { createProxyMiddleware } = require("http-proxy-middleware");

module.exports = function (app) {
  app.use(
    createProxyMiddleware("/api", {
      target: "http://localhost:8000/",
      pathRewrite: {
        "^/api": "",
      },
    })
  );
};
```

## File Watcher

It has file watcher Algorithm written in **_C++_**

Based on events and metadata from these files, Parcel determines which files need to be rebuilt.

### Known issues with File Watcher

- Safe Write (Editor and IDE's come with this feature)
- Linux : no space left on device
- Keep development folder inside **Dropbox, Google Drive or other cloud storage** solutions



## Auto Install

Install plugins based on project configuration.
* eg. if parcel found **.sass** file it will install the **@parcel/transformer-sass** plugin

Auto install occurs only in **DEVELOPMENT** mode.
> To disable auto install use  below flag
>
> --no--autoinstall

If any plugin or dependency missing in **production** build , It will fail.


