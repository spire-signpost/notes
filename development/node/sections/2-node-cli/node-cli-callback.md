### Node.js - Guide
#### section 2 - cli - `basic-callback` test app

A brief example app for Node.js command line development and usage.

#### Contents
* Intro
* simple callback example

#### intro
A simple app to test callback usage and structure.

A *callback* function is passed as an argument to another function, and is then executed after some defined event, action, request has happened. e.g. a timer, remote request, event listener for the DOM &c.

The point of a callback is to actually do something with the return for the action &c.

##### simple callback example
A contrived, simple example of callback definition and usage might be as follows,

```js
var getBook = (id, callback) => {
    var book = {
        book_isbn: id,
        title: "Hannibal's Footsteps"
    };
    callback(book);
};

getBook(270797, (book) => {
  console.log(book);
});
```

The `getBook()` function is called with an ID for the book, and the function we'll use as the callback. In this example, the callback function will simply output the passed `book` to the console.

In the `getBook()` function itself, it expects two parameters,

  * an `id` for the requested book
  * the function to call as the callback - expects one parameter to be passed when called...

In this `getBook()` function, we might query a DB for the book object, or perhaps request it from a remote API. In this example, we simply create a dummy `book` object, which we can then pass to the `callback` parameter.

The defined function for the `callback` parameter will now output the expected passed book object to the console.
