# Bundle Inlining

In simple words, `complile whatever content` in side a bundle along with it's `resolved dependencies`, and `inline` it into another bundle (let's say `parent bundle`).

## Inlining a bundle as TEXT

`bundle-text:` : Scheme used to inline the content as `plan text`.

```js
import cssText from "bundle-text:./test.css"; //Inlining bundle as plan text

// inject <style> tag
let style = document.createElement("style");
style.textContent = cssText;
shadowRoot.appendChild(style);
```

## Inlining as Data URL

`data-url:` : Scheme allows us inlining a bundle as a `data-url`

- `Image/binary-data`

  ```css
  .foo {
    background: url(data-url:./background.png);
  }
  ```

  - `data:[<mediatype>][;base64],<data>`

  - Example
    `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAWklEQVR4nGJYiGIlYKAhgXF2BgUFfPz/+/fxUoaKCggEH9+/ftXHxhYGBgYGRkZP8/NzcTExODg4N/f39/j7+zsbHw8PDxcXHx8fX19fz9/fXu7vDw8Pj4+Pb29vHx8fz8/Pz8/v7/Pz8+fj4+fn5/v7+7u7v39/f7+/gZGBsbGxv7+/ujo6Ozs7PT09Pf39/3zcnJ3t7e4ODg+Li4uXl5evr6/Hx8fX19f39/f39/f38fHx7e3t/f39/////ftYBbQG9T4vgAAAABJRU5ErkJggg==
`

- Other files ( HTML, CSS, JavaScript, and other textual formats)

  - Data URL will represent the file's content as a `URI-encoded string` rather than base64-encoded

  - Example

    `data:text/css;charset=utf-8,body%20%7B%0A%20%20background%3A%20red%3B%0A%7D`

More Information

### [Bundle Inlining doc](https://parceljs.org/features/bundle-inlining/)
