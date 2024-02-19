# Code Splitting

Split your application code into seperate **Bundles**

These bundles loaded **On demand**

Small bundle size and faster load time.

```
async function load() {
  const page = await import("./pages/about"); //returns a promise
  // Render page
  page.render();
}
load();
```

# Tree Shaking

When parcel can detect **which exports of dynamically imported module you use**

Parcel will **Tree shake** the **unused** exports.

Tree shaking works with **static property access or destructuring** with either **await or .then()** syntax.

```js
let { x: y } = await import("./b.js");

let ns = await import("./b.js");
console.log(ns.x);

import("./b.js").then((ns) => console.log(ns.x));

import("./b.js").then(({ x: y }) => console.log(y));
```

# Shared bundles

If multiple parts of your application depend on the same common modules, those modules will be split out into a **shared bundle**

## Configuration

```json
//package.json
{
  "@parcel/bundler-default": {
    "minBundles": 1, // Threshold - For an asset to be split, it must be used by more than minBundles bundles.
    "minBundleSize": 3000, //For a shared bundled to be created, it has to be at least minBundleSizebytes big (before minification and tree shaking)
    "maxParallelRequests": 20 //To prevent overloading the network with too many concurrent requests, this ensures that a maximum of maxParallelRequests sibling bundles can be loaded together.
  }
}
```

[Learn more about web optimization : web.dev](https://web.dev/granular-chunking-nextjs/)

# Internalized async bundles

If same module imported as **synchronously** & **asynchronously** whthin a bundle.

Rather than splitting out asynchronous module as seperate bundle, Parcel will make asyn dependency **Internalized**

```js
// main.js (after bundling)

// Synchronous import
import { syncFunction } from "./module";

// Asynchronous import internalized
const asyncModulePromise = new Promise((resolve) => {
  import("./module").then((module) => {
    resolve(module);
  });
});

syncFunction();
asyncModulePromise.then((module) => {
  module.functionFun();
});
```

# Deduplication

If dynamically imported module has a dependency which is already available in all of it's possible ancestries, it will be deduplicated.
