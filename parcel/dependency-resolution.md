# Dependency Resolution

## Dependency Specifiers

Simple way it means **PATH**

A string that describes the **location of dependency** relative to file that imports it.

### Relative specifiers

**Relative PATH**

It resolve file relative to the importing file

```js
import "./module.js";
import "../constant.js";
```

### File extensions

Recommended to include full file extension in all import specifiers.

For compatibility with **CommonJs in Node**, and with **TypeScript**, Parcel allow to ommit
file extesions for certain file types.

> **.ts, .tsx, .mjs, .js, .jsx, .cjs, json**

```js
import "./utils"; // import './utils.js'
import "../constants";
```

for other files extension is required.

### Directory index files

In JS based languages, dependency specifiers may resolve to direcotory rather than a file.

1. If direcotory contain **package.json**, main entry will be resolved based on config's inside package.json

2. If no package.json present, It will attempt to resolve **index** file within directory (**index.js/index.ts**)

```js
// /path/to/project/src/app.js:

import "./client"; // It will be resolved to '/path/to/project/src/client/index.js'
```

## Bare specifiers

Bare specifiers starts with any character except `.` ,`/`,`~`,`#`

In JS based languages they **resolved** to package in **node_modules**

```js
//  /path/to/project/src/client/index.js:

import "react";

// above line of code may resolve like
// /path/to/project/node_modules/react/index.js
```

node_modules directories are **searched upwards**
from importing file.

If importing file is at _/path/to/project/src/client/index.js_

That time following location would be searched.

- /path/to/project/src/client/node_modules/react
- /path/to/project/src/node_modules/react
- /path/to/project/node_modules/react

### Package sub-paths

Bare specifiers may also specify a **_sub-path_** within a package.

Means package may provide multiple entry points.

```js
import "lodash/clone";
// It will resolve something like
//  node_modules/lodash/clone.js
```

### Builtin Modules

Parcel uses **shims/polyfills** (mock/alternative implementaion) for buit-in modules.

Shims/Polyfills used to make code compatible with node.js and Web environments.

## Absolute Specifiers

Starts with `/`

Resolves file with relative to **project root**.

#### Which is **Project root/Base directory** of your project !?

Directory which contain

- Package manager's `.lock` file. ( `yarn.lock` or `package-lock.json`)

- Source controller direcotory `.git`

```js
import "/src/client.js";

// It might resolve to   '/path/to/project/src/client.js'
```

## Tilde Specifiers

Starts with `~`

Resolve file relative to **package root**

#### Which is **Package root** ?

- Direcotory with `package.json` file.

> /path/to/project/packages/frontend/src/client/index.js:

```js
import "~/src/utils.js";
```

> It would resolve to `/path/to/project/packages/frontend/src/utils.js`

## Hash Specifiers

Starts with `#`

Behavior depends on the file type they are contained within.

In JS and TS files

- It treated as **Internal package imports**

```
/project
├── src
│   ├── index.js
│   └── utils.js
└── package.json
```

```js
// src/index.js

import "#utils";
```

- Importing the utils.js module from the same package or project

In other files

- Treated as **relative URL hashes**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <a href="#section">Go to Section</a>
    <div id="section">
      <!-- Section Content -->
    </div>
  </body>
</html>
```

- Clicking the link will scroll to the section with the id="section" within the same HTML document

## Query Parameters

Specify transformation options for resolved files.

```css
.logo {
  background: url(logo.png?width=400&height=400);
}
/* width & heigt are query-parameters */
```

> Note: Query parameters are not supported for CommonJS specifiers (created by the require function).

## URL Schemes

Dependency specifiers Use it to target **Named Pipelines**

`.parcelrc:`

> ```json
> {
>   "extends": "@parcel/config-default",
>   "transformers": {
>     "arraybuffer:*": ["...", "parcel-transformer-arraybuffer"]
>   }
> }
> ```

`src/example.js:`

> ```js
> import buffer from "arraybuffer:./file.png";
> ```

#### Reserved URL schemes

- `node`: – an alternative way of specifying a builtin Node module.

  ```js
  import "node:path";
  ```

- `npm`: – a way for URL dependencies (e.g. in HTML, CSS, or web workers) to import files from a node_modules package.

  ```html
  <link rel="stylesheet" href="npm:bootstrap/dist/css/bootstrap.min.css" />
  ```

- `http:` and `https:` – a fully qualified URL dependency. These are resolved at runtime, and left untouched by Parcel.

  ```html
  <script src="https://cdnjs.cloudflare.com/ajax/libs/react/17.0.2/umd/react.production.min.js"></script>
  ```

- `data`: – A data URL including the dependency source code inline. Currently not implemented by Parcel, but reserved for future use.

- `file`: – A file URL. Reserved for future use.

- `mailto:` and `tel:` - Commonly used URL schemes. These are left untouched by Parcel.

  ```html
  <a href="mailto:example@example.com?subject=Feedback">Send Feedback</a>
  <a href="tel:+123456789">Call Us</a>
  ```

## Glob specifiers

Importing multiple files at once via globs

To unable it add `@parcel/resolver-glob` to your `.parcelrc`

`.parcelrc:`

> ```json
> {
>   "extends": "@parcel/config-default",
>   "resolvers": ["@parcel/resolver-glob", "..."]
> }
> ```

## Package Entries

When resolving **packge direcotory** , `package.json` file is consulted to determine package entry.

Parcel wil check for below fields (**In Order**)

- `source:` A **monorepo** or via `npm link`

  ```go
  /my-package
  ├── package.json
  └── src
   ├── index.js
   └── utils.js
  ```

  ```json
  // package.json
  {
    "source": "src/index.js"
  }
  ```

- `exports:`

  ```go
  /my-package
  ├── package.json
  ├── lib
  │   ├── index.js
  │   └── utils.js
  └── src
    ├── index.js
    └── utils.js
  ```

  ```json
  // package.json
  {
    "exports": {
      ".": "./src/index.js"
    }
  }
  ```

- `browser:` If building for browser environment _browser_ field **overrides** other fields.

  ```go
  /my-package
  ├── package.json
  ├── index.js
  └── index.browser.js
  ```

  ```json
  // package.json
  {
    "browser": "index.browser.js"
  }
  ```

- `module:` and `main:`

  - module : ES module version
  - main : CommonJS version

  ```go
  /my-package
  ├── package.json
  ├── index.js
  └── index.mjs
  ```

  ```json
  // package.json
  {
    "module": "index.mjs",
    "main": "index.js"
  }
  ```

- `Fallback to Index file` If none of the above fields are set, Parcel falls back to `index` file within directory.

  ```go
  /my-package
  ├── package.json
  └── index.js
  ```

## Package Exports

`exports` field in `package.json` used to defined, publically accessible **entry points** for a package.

### Enabling package exports

- It is **diabled** by default, it might break project that wasn't designed with it in mind.

  ```json
  {
    "@parcel/resolver-default": {
      "packageExports": true
    }
  }
  //   /path/to/project/package.json:
  ```

### Single export

If a package has only a single exported module.

```json
{
  "name": "foo",
  "exports": "./dist/index.js"
}
// node_modules/foo/dist/index.js
```

### Multiple exports

If package exports multiple modules.

```json
{
  "name": "foo",
  "exports": {
    ".": "./dist/index.js", // "." defines entry points
    "./bar": "./dist/bar.js" // sub path
  }
}
```

> `foo` resovles to `node_modules/foo/dist/index.js`

> `foo/bar` resolves to `node_modules/foo/dist/bar.js`

- Wild-card export

  All `.js` files for `dist` folder are exported.

  ```json
  {
    "name": "foo",
    "exports": {
      "./*": "./dist/*.js"
    }
  }
  ```

  `"foo/bar"` resolves to `node_modules/foo/dist/bar.js`

## Conditional Exports

Different mapping for same specifier in different environments.

```json
{
  "name": "foo",
  "exports": {
    "node": "./dist/node.js",
    "default": "./dist/default.js"
  }
}
```

- Nested conditional exports

  ```json
  // pacakage.json
  {
    "name": "foo",
    "exports": {
      "./bar": {
        "node": "./dist/bar-node.js",
        "default": "./dist/bar-default.js"
      }
    }
  }
  ```

### `The order that exports conditions are resolved follows the order they are defined in the 'package.json'`

Parcel supports following export conditions:

- `"import"` – Package was referenced using the ESM import syntax.
- `"require"` – Package was referenced using the CommonJS require function.
- `"module"` – Package was referenced from either the ESM import syntax or the CommonJS require function.
- `"sass"` – Package was referenced from a Sass stylesheet.
- `"less"` – Package was referenced from a Less stylesheet.
- `"stylus"` – Package was referenced from a Stylus stylesheet.
- `"style"` – Package was referenced from a stylesheet (e.g. CSS, Sass, Stylus, etc.).
- `"node"` – Output will run in a Node environment.
- `"browser"` – Output will run in a browser environment.
- `"worker"` – Output will run in a web worker or service worker environment.
- `"worklet"` – Output will run in a worklet environment.
- `"electron"` – Output will run in an Electron environment.
- `"development"` – Package was loaded in a development build.
- `"production"` – Package was loaded in a production build.
- `"default"` – The default condition in case no other conditions matched.

## Aliases

Used to override the normal resolution of a dependency.

### Package aliases

To map a `node_modules` dependency to different package or to `local file`.

1. mock with another package

```json
//mock to different package
{
  "alias": {
    "react": "preact/compat",
    "react-dom": "preact/compat"
  }
}
```

2. mock with local file

```json
{
  "alias": {
    "react": "./my-react.js"
  }
}
```

### File aliases

We can mock one file with other using `alias` or conditionally according to perticular environment.

```json
{
  "browser": {
    "./fs.js": "./fs-browser.js"
  }
}
//  my-module/fs.js => resolve to =>  my-module/fs-browser.js
```

### Glob Alias

Use to replace multiple files or paths using **wildcard** patterns.

```json
{
  "source": {
    "./dist/*": "./src/$1"
  }
}
```

The `*` wildcard `captures the filename` in the dist directory, and `$1` refers to this `captured filename`

### Shim aliases

`Fiels or Packages` can aliased to `false` to be excluded from the build and replaced with an empty module.

```json
{
  "alias": {
    "fs": false
  }
}
```

### Global aliases

Used to aliases packages and libraries to `global` variables. If library loded from CDN, once it resolved , it will be replaced with global variable.

```json
{
  "alias": {
    "jquery": {
      "global": "$"
    }
  }
}
```

```js
import $ from "jquery";

// trasformed into such way

const $ = window.$; // Assuming $ is globally available as a property of the window object
```

## TSConfig

`tsconfig.json` is config file for `TypeScript`

Parcel searches **upward** from the file containing the dependency to find the nearest `tsconfig.json`.

`extends` : Option to `merge` the settings from multiple `tsconfig.json` files.

- `baseUrl` : Defines the `base directory` from which to resolve.

  ```json
  {
    "compilerOptions": {
      "baseUrl": "./src"
    }
  }
  //import 'Home';=> resolves to => src/Home.js => if src exists => fall back => node_modules/Home
  ```

- `paths`: Used to define mapping from `base specifiers to file paths`

  - `paths` field is relative to `baseUrl` if defined, otherwise to the direcotory containing the `tsconfig.json` file.

```json
//tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "jquery": ["./vendor/jquery/dist/jquery"],
      "app/*": ["./src/app/*"]
    }
  }
}
```

```js
//src/App.js

import "jquey"; // resolve to => ./vendor/jquery/dist/jquery.js
import "app/foo"; // resolve to => ./src/app/foo.js
```

- `moduleSuffixes`: Specify the file name `suffixes` to search while `resolving` a module.

```json
//tsconfig.json
{
  "compilerOptions": {
    "moduleSuffixes": [".ios", ".native", ""]
  }
}
```

```js
// src/App.js:

import "./foo"; //Parcel will look for ./foo.ios.ts, ./foo.native.ts, and ./foo.ts
```
