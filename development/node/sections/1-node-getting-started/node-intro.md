### Node.js - Guide
#### section 1 - getting started - intro and install

A brief intro Node.js.

#### Contents
* Intro
* Why use Node.js?
* Installation
  * Homebrew on OS X
  * test Node.js
* Node.js and NPM
  * first steps with node.js
* `basic-require` test app
  * require - intro
  * require - loading bundled Node.js modules
  * require - load custom local files
  * require - load third-party modules from NPM
  * require - using a third-party module
* monitor app development state - using `nodemon`

#### Intro
Node.js allows us to run JavaScript based apps outside of the context of a standard web browser or web view.

For example, we can now write Node.js based apps to manipulate local filesystems, query DBs, run web servers, and so on.

Node.js and JS in a browser, for example, are both running Chrome's V8 JS engine - node.js is very fast at executing and running.

(n.b. V8 is written using C++ code.)

#### Why use Node.js?
One of the primary benefits of node.js is its *event-driven, non-blocking I/O model*.

I/O (input and output) = usually as simple as *reading* and *writing*, including to and from a database, updating a file, querying a remote service...

non-blocking = multiple instances, events &c. running without blocking each other

e.g. a blocking script may follow a pattern such as,

  * get and wait for username1 to return from DB
  * then print out username
  * then get and wait for username2 to return from DB
  * then print out second username
  * then combine username1 and username2...
  * then print out a mathematical sum of numbers (e.g. 2 * 3 = 6)

In a blocking app, our system has to wait for each step to complete. In effect, wait for the DB to return, print to screen, &c. No other processes may be completed using the JS engine.

By contrast, a non-blocking script in node.js is able to use the *event loop* to manage multiple events. So,

  * start process to get first username from DB (I/O operation...)
  * start other I/O operations, including fetching another username from the DB
  * other functions and operations may also be executed as we wait for the return from the I/O operations, e.g.
    * perform a mathematical calculation
    * print some output to the console
    * ...
  * then, as the I/O operations with the DB complete, output return to the console &c.

Benefits of non-blocking include,

  * greater speed for I/O based operations
  * system is not blocked for requests
  * multiple operations may be executed - not blocked by waiting for callbacks...

Node.js is single-threaded, due to JS, so events instead of threads helps with system resource usage, speed, performance...

#### Installation
Unless an earlier version is specifically required, install the latest version from the Node.js website or using a package manager for your chosen OS.

  * https://nodejs.org/en/

There is also the option of a LTS version or the latest features release.

##### installation - Homebrew on OS X
Homebrew package manages is also available on OS X,

  * https://brew.sh/

A useful FAQ for Homebrew is available,

  * http://docs.brew.sh/FAQ.html

Then install Node.js using,

```
brew install node
```

or upgrade an existing installation,

```
brew update
brew upgrade node
```

n.b. install the latest version for most project development.

##### installation - test node.js
A quick check that node.js is now installed successfully,

```
node -v
```

prints the current version of Node.js installed on the system.

#### Node.js and NPM
NPM = node package manager

Node.js community and NPM is a great bonus to using Node.

NPM site is available at the following URL,

  * https://www.npmjs.com

Lots of modules available for use in a node based app.

##### node.js and npm - first steps with node.js
Start node.js at the command-line,

```
$ node
>
```

At the interactive prompt, we can start using JavaScript code. In effect, Node.js converts this JS code into machine code that the host system understands and runs. e.g.

```
> console.log("node.js test output...");
node.js test output...
```

We can do exactly the same thing using the *console* in the developer tools of a browser. However, there are differences due to context. e.g. browser has a global object, `window`. Node.js has `global`. Browser also has the DOM element `document`. Node.js has a basic equivalent, `process`. e.g.

```js
process.exit();
```

So, we can exit the running processes in Node.js similar to Ctrl+c.

#### `basic-require` test app

A simple app to test usage of `require`.

##### require - intro

Require in Node.js allows us to work with modules.

Modules bring together perceived contiguous functions into a unified group.

Require allows us to do three primary things,

  * load modules bundled with Node.js
    * e.g. 'HTTP' for a web server 'fs' for working with the file system
  * import third-party modules
    * e.g. 'ExpressJS'...
  * create modularity within our own apps
    * break up an app into constituent parts to help with abstraction and modularity

##### require - loading bundled Node.js modules

Check current bundled Node.js modules,

  * https://nodejs.org/api/

e.g. File System, OS...

Import modules &c.

e.g. fs - filesystem

```js
const fs = require("fs");
```

`const` used as we're not modifying value of variable whilst app running. Then call available methods from module, e.g.

```js
fs.appendFile(...);
```

Update for ES6 to avoid error due to missing third argument,

```js
// call method from fs module - es6 compatible
fs.appendFile('test-note.txt', 'welcome to node note...', function(error) {
    if (error) {
        console.log("error - unable to append to file...");
    }
});
```

This will now create a new text file, if it doesn't already exist, and append the text string to the end of the file.

##### require - load custom local files

With `require`, we can also load our own files within an app. Any custom functions and properties stored in these local files will now be available to our app's project.

```js
const local = require("./custom.js");
```

This will load the file `custom.js` from the relative position to the root of the project directory.

Within this `custom.js` file, we can now specify some functions and properties, which become available as a property of `module.exports`. (object module with child object exports...)

**n.b.** `module` is available in all node.js modules, files &c.

e.g.

```js
module.exports.createNote = () => {
    ...
}
```

We can use a simple arrow function to define our `createNote()` function, which is now a property of the `exports` object of the overall `module` object.

##### require - load third-party modules from NPM
Also install and load third party modules from NPM.

Per app, start by setting up NPM in the project's root directory. Issue the following command,

```
npm init // checks project install &c.
```

Use this command to follow a walkthrough for setting up your project's `package.json`, which is added to the root of the directory.

This `package.json` file also stores references to each third party module we install for our app. It keeps a record of these modules in this file.

To install a third party module, such as *lodash*, use the following command,

```
npm install lodash --save
```

The flag `--save` automatically adds details of this new module to our project's `package.json` file in an object called `dependencies{}`.

We can then use our app's `package.json` file retrospectively to update and install our app with the required modules. This is useful if we share our app or simply move it to a different directory &c.

e.g. share app with `package.json` file, and remove the `node_modules` folder. Then, use the following command whilst in our app's root directory,

```
npm install
```

This will use NPM to fetch all of the specified dependencies in the app's `package.json` file.

##### require - using a third-party module
Follow standard pattern to `require` a third party module, e.g.

```js
// require third party modules from NPM
const _ = require("lodash");
```

We can then use the methods &c. for this module, e.g.

```js
_.isString("hello world");
```

This helper method checks that the passed data is a string - returns a boolean e.g.

```js
// using lodash
console.log(_.isString("hello world...")); // returns boolean for `true`
```

Another useful example for *lodash*,

```js
// using lodash - check unique values in array
var checkArray = _.uniq(["hello", "world", "hello", "world", "again", "1997"]);
console.log(checkArray);
```

#### monitor app development state - using `nodemon`
A useful third party module for local development is `nodemon` - keeps node app running and monitors it for changes.

Then app is restarted automatically for each change.

Install `nodemon` globally using the following command,

```
npm install -g nodemon
```

Then, start an app using the following command

```
nodemon app.js
```
