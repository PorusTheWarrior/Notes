# JSX

- Java Script XML
- Java Script Syntax eXtension (formally)
- JS extension that allow creation of `Document Object Model (DOM)` trees using XML like syntax.
- created by `Facebook` for use with `React`
- `Syntactic sugar`, JSX is `transpiled` into `nested JS` function calls

> const heading = \<h1>JSX element\</h1>

## Things to understand while working with JSX

### Nested Elements

- Multiple elements at same level must be `wrapped` inside single React element like `<div>` or `<Fragment>` or `<></>`

  - ```js
    const App = () => {
      return (
        <div>
          <p>Header</p>
          <p>Content</p>
          <p>Footer</p>
        </div>
      );
    };
    ```

### Attributes

- use to pass data to component
- we can pass custom attributes also
- all attributes received by component as `props`

  - ```js
    const MyComponent = (props) => {
      return <>I got the props {props.name}</>;
    };

    <MyComponent name="shubham" />;
    ```

### Java Script Expressions

- Java Script `expressions` but not the `statements` can be used inside JSX with `curly brackets` `{}`

```js
<h1>{5+5}</h1>

// above element would be render as
<h1>11</h1>

```

```js
<h2>{console.log("We also can log, isn't it cool! ")}</h2>

<h3>{(console.log("hello"), console.log("second line"))}</h3>

<div> {(() => console.log(8))()}</div>
// above example log into console
```

#### Note: Whatever the `piece of code` you write inside `{}` in JSX , should be `resolved` to be an `expression`.

### Conditional Expressions

- we can't use `if-else` statements inside JSX.
- Instead use `Conditional Expressions`

```js
<h1> {userId === 5 ? "valid" : "invalid"} </h1>
```

## Transpilation of JSX

- JSX code will be transpiled to JS objects

```js
const root = <h1>Hello, world!</h1>;
console.log(root);
```

![JSX-to-JS-object](../assets/jsx-to-js-object.png)


### Fun Fact !!!

#### What is Virtual DOM look like and where is it!?

- The JS `Object` inside Above Screenshot is noting but `Virtual DOM` !!!!

- React will store the copy of JS object got from `<APP/>` or root elemet somewhere inside memory.

## Resource links:

- [JSX (JavaScript) - Wikipedia](https://en.wikipedia.org/wiki/JSX_(JavaScript)#:~:text=Initially%20created%20by%20Facebook%20for,similar%20to%20the%20original%20JSX.)