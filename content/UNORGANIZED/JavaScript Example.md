---
publish: true
---
```js
// Example JS file
// This is an example of a JS file that will be included in the build
// It can be included in a page using the following:
// <script src="example.js"></script>
// It can also be minified using the following:
// <script src="example.min.js"></script>

// This is a simple function that will be included in the build
function exampleFunction() {
  console.log('This is an example function');
}

// This is a class with variables and functions that will be included in the build
class ExampleClass {
  constructor() {
    this.exampleVariable = 'This is an example variable';
  }

  exampleFunction() {
    console.log(this.exampleVariable);
  }
}

```