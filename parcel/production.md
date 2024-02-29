# Production

Parcel's production mode automatically `bundles and optimize` your app for production.

command :

```go
parcel build src/index.html
```

## Size Optimization

Used to reduce bundle size.

### Minification

- Remove whitespace
- Rename variables to shorter names
- Function and method renaming

#### Tools to minify

- `JavaScript` : [SWC](https://swc.rs/)

- `CSS`: [lightningcss](https://lightningcss.dev/)

- `HTML`: [htmlnano](https://github.com/posthtml/htmlnano)

- `SVG`: [SVGO](https://github.com/svg/svgo)

### Tree Shaking / Dead code elimination

In `production` build, parcel statically analyzes the `imports and exports` of each module and `remove evrything that isn't used`.

- It's for both `Static` and `Dynamic` imports
- Works for `ES modules`, `CommonJS` and even across languages with `CSS` modules.

When possible parcel do `Scope Hoisting`

- Concatenates modules into `single scope`, rather than wrapping each module into seperate function.

### Development/Dead Branch Removal

Labraries gives some `debugging features` in deveplopment mode. In `production` mode parcel `stripped` all the `dead branches` and reduce bundle size.

```js
if (process.env.NODE_ENV !== "production") {
  // Only runs in development and will be stripped in production builds.
}
```

### Image Optimization

Parcel supports `resizing`, `converting` and `optimizing` images.

Image optimizaton done in both `DEV` and `PROD` enviornments.

Use `query params`(formatting and resizing) when image referencing inside `HTML`,`CSS` and `JS`.

```html
<picture>
  <source
    type="image/webp"
    srcset="image.jpg?as=webp&width=400, image.jpg?as=webp&width=800 2x"
  />
  <source
    type="image/jpeg"
    srcset="image.jpg?width=400, image.jpg?width=800 2x"
  />
  <img src="image.jpg?width=400" width="400" />
</picture>
```

Parcel provides `lossless`(reduce image size without loosing quality) image optimization for JPEGs and PNGs in `PROD` mode.

### Differential Bundling

Parcel automaticall produces `<script type="module">` with modern browsers and a fallback `<script nodemodule>` for legecy/older browsers when necessary.

- Reduce bundle size by avoiding `transpilation`
- Browsers target difined inside `browserlist` field in `package.json`

### Compression

Parcel supports bundle compression with help of [Gzip](https://en.wikipedia.org/wiki/Gzip) and [Brotli](https://en.wikipedia.org/wiki/Brotli)

```json
// .parcelrc:
{
  "compressors": {
    "*.{html,css,js,svg,map}": [
      "...", // original uncompressed bundle
      "@parcel/compressor-gzip", // .gz
      "@parcel/compressor-brotli" // .br
    ]
  }
}
```

- you’ll get a `.gz` and a `.br` file along side the original uncompressed bundle

## Cache Optimization

### Content Hashing

- Parcel automatically includes `content hashes` in the names of all O/P files.

- It unables long-term `browser cashing`

- ASA `contents` of bundle changes, `hash` included in the `filename` will be `updated`.

- It triggering `invalidation` of CDN and Browser `cashes`.

### Cascading Invalidation

- `Cascade` : series of steps
- Should avoid it.
- If some content inside a file/bundle changes, the chache invalidation cycle triggers.
- The file/bundle(content modified) and `dependend` files/bundles will also get `invalidate`.

#### Whats the bad thig about `Cascading Invalidation`?

- A tiny code change made to just one source file - invalidate major portion of bundle.

#### How can we avoid Cascading Cache Invalidationn

- Import Maps
- Service worker
- Custom script-based loaders

Read more about [Cascading Cache Invalidation](https://philipwalton.com/articles/cascading-cache-invalidation/)

#### What parcel does to avoid `Cascading Invalidationn`

- Uses a `manifest` in each `entry` bundle.
- Manifest file contain `mapping of stable bundle ids` to `final content hashed` filenames.
- Parcel uses that bundle id insted of hashed names

#### Split manifest into seperate bundle

- Threshold is `100KB`

- As mapping inside `manifest` might chages `frequently`. It leads to invalidate `etire bundle`.

- To avoid above thing, Parcel split the manifest into seperate bundle.
- ```json
  // To set threshold for splitting manifest
  // package.json
  {
    "@parcel/runtime-js": {
      "splitManifestThreshold": 10000
    }
  }
  ```

### Shared Bundles

- Automatically indentify `common dependency` between multiple entry points.
- Seperate that `dependency` into `separate` bundle
- Reduce `duplication` and improves `cacheability`
