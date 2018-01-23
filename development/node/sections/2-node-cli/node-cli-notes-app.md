### Node.js - Guide
#### section 2 - cli - `node-notes` test app

A brief example app for Node.js command line development and usage.

#### Contents
* Intro
* user input
  * command line arguments
  * `process` object
  * multiple arguments
  * working with the `yargs` module
  * yargs passing argv
  * create method for adding a new note
* JSON
  * working with strings and objects
  * write data to a file
  * read data from a file
* create notes
  * intro
  * save single note
  * catch basic errors
  * check duplicate note titles
* code refactor - part 1
  * intro
  * fetch and save
  * return notes
* delete notes
  * intro
  * delete using filter()
  * check if note deleted
* read note
  * intro
  * get a single note
* code refactor - part 2
  * console statements for a note
* debugging
  * intro
  * using the command line
  * using repl
  * using Chrome Developer tools
* listing notes
  * intro
  * fetch all notes
* advanced yargs
  * intro
  * configuring yargs in app.js
  * help the user
  * refactor code

#### intro
A command line note-taking app to test various concepts with general development of Node.js app.

##### user input - command line arguments
Node.js allows us to pass any required arguments at the command line, e.g.

```
node app.js add
```

With this command we are starting our app, and passing an argument string `add`.  

We can then process this argument within our Node.js app.

##### user input - `process` object

In our app, we can use the `process` object with the `argv` property to get details on the passed arguments.

e.g.

```
console.log(process.argv);
```

*n.b.* `argv` stands for *arguments vector*, or more correctly for JS *arguments array* is probably closer to the result.

This command returns an array of arguments passed to the command line, including the user input argument `add`.

We can double check the passed user input argument,

```js
// get arguments from command line
var cmdarg = process.argv[2];
// test output from cli arguments
console.log('Command: ', command);
```

which returns the command input as `add`.

Then add a conditional check for user input command, e.g.

```js
// check user input and return conditional response
if (cmd === 'add') {
    console.log("add note command requested...");
} else if (cmd === 'delete') {
    console.log("delete note command requested...")
} else {
    console.log("no command requested...")
}
```

##### user input - multiple arguments

A user may also pass multiple arguments to the command line. e.g.

```
node app.js add --title="test note 1"
```

If we then check the return of the `argv` array, we'll see multiple commands returned for user input.

However, this will return a value such as

```js
[
    ...,
    'add',
    '--title=test note 1'
]
```

This works, but it will require careful parsing of the return values for the passed arguments.

##### user input - working with the `yargs` module

A better way to work with user input is with the third party module, `yargs`.

Yargs is a node.js utility library for parsing data, including strings. Further information on the project's GitHub page,

  * https://github.com/yargs/

Install this node.js module using the standard command,

```
npm install yargs --save
```

Then we can `require` it in our app,

```
const yargs = require("yargs");
```

##### user input - yargs passing argv

As with Node's `process.argv`, yargs also provides its own return arguments as `yargs.argv`, which has been processed for easier usability within an app.

We can store this return as follows,

```
const argv = yargs.argv;
```

The main difference between `process.argv` and `yargs.argv` is the structure yargs returns for the arguments. Yargs returns a useful object with an array for the arguments passed by the user input.

Then, as a user enter key:value pairs, yargs will parse and return a property and value. e.g.

```js
Yargs { _: ['add'], title: 'a new note', '$0', 'app.js'}
```

If we then need to get the title of the new note, we simply call the title property of the yargs argv object.

e.g.

```js
console.log("note title = ", argv.title);
```

for the command,

```
node app.js add --title="a new note"
```

##### user input - create method for adding a new note
We can use these passed arguments with custom methods to create a new note, delete a note, and so on.

In our app.js file, we check for the requested action, e.g. add a new note,

```js
// check user input and return conditional response
if (cmd === 'add') {
    console.log("add note command requested...");
    notesio.createNote(argv.title);
}
```

and then pass the value of the input title as an argument for the new note to a `createNote()` function. This function is stored in the `notes_io.js` file for the app.

Our new function is as follows,

```js
// api - create note function
var createNote = (title) => {
    console.log("create note...", title);
    // add logic to create new note...
};
```

Then, we need to add this function to an object for the `module.exports` of this file, e.g.

```js
module.exports = {
    createNote: createNote
};
```

Using ES6, we can update this output due to the replication of property and value in the object, e.g.

```js
module.exports = {
    createNote
};
```

We can then update any other commands and functions using a similar pattern.

##### JSON - working with strings and objects
JSON - JavaScript Object Notation

Helps us represent JS arrays and objects as strings.

In our JS files, we can represent data as objects, e.g.

```js
// create simple object
var testObj = {
    author: 'daisy'
}:
```

If we then want to use this object, such as send it to another server, save to a file &c. we can use the JSON method, `stringify()`, e.g.

```js
var testStringObj = JSON.stringify(testObj);
```

This variable is now a string, and no longer an object.

We can test this string as follows,

```js
// test string
console.log(typeof testStringObj);

// output string
console.log(testStringObj);
```

and the console will show that type is string, and then print the string, e.g.

```js
{"author":"daisy"}
```

JSON is also able to store more than just strings in an object. It can store other objects, numbers, arrays, booleans &c.

We can also work with strings to objects, in effect reversing the `stringify()` process above.

e.g.

```js
var authorString = "{"author":"yvaine"}"
```

which we can then parse from a string to a JSON object,

```
// parse string back to JSON object
var author2 = JSON.parse(authorString);
```

Again, this can be tested at the console,

```js
// test JSON object
console.log(typeof author2);
//output author2 object
console.log(author2);
```

##### JSON - write data to a file
We need to `require()` the `fs` module so we can work with files.

```js
const fs = require('fs');
```

Then, we can define our test note object, e.g.

```js
var testNote = {
    title: 'note 1',
    author: 'daisy'
};
```

stringify it ready to be stored in a file,

```js
// stringify object to save to file
var testNoteString = JSON.stringify(testNote);
```

and then use the FS module to write the string to the file,

```js
// write string to file using FS module
fs.writeFileSync('notes.json', testNoteString);
```

##### JSON - read data from a file
We can also read data from a JSON file, e.g.

```js
// read string from notes.json file
var noteJSON = fs.readFileSync('notes.json');
```

which then needs to be parsed so we can use the return object,

```js
// parse string to object to be used in app...
var note = JSON.parse(noteJSON);
```

This can be tested briefly using the console,

```js
// check return testNote object
console.log(typeof note);
console.log(note.title, note.author);
```

##### create notes - intro
Our user needs to be able to create a new note, which is then automatically saved or written in a persistent state to a defined JSON file.

This creation and persistency will be added to the existing `createNote()` function.

##### create notes - save single note
We can initially update this function to store all notes, and create a single note object, e.g.

```js
// array to store all notes
var notes = [];
// note object - using ES6 syntax...
var note = {
    title,
    author
};
```

The single note will be added to the specified notes,

```js
// add new note to existing notes array
notes.push(note);
```

We can then write this data to a specified JSON file,

```js
// write note to notes JSON file
fs.writeFileSync('notes.json', JSON.stringify(notes));
```

However, currently this will simply overwrite the file each time we create a new note.

##### create notes - catch basic errors
We need to be able to catch basic errors, including a missing JSON file for saving the app's notes.

Add a `try-catch` block, where we can simply try to create a JSON file and then catch any errors that may arise.

We'll add this initial error checking to the existing `createNote()` function.

```js
// code to check for an error
try {
  // read existing notes from JSON file
  var currentNotes = fs.readFileSync('notes.json');
  // parse notes string...
  notes = JSON.parse(currentNotes);
} catch (error) {
  // if error, catch will be run...
}
```

Basically, we try to read the notes JSON file. If successful, we update our notes value to the current notes in the file. This means we get the current notes stored in the file before then adding a new one. If there is no file, we catch the error and move on in the app's execution.

##### create notes - check duplicate note titles
We need to be able to loop through all of the current notes, and check for existing note titles that match the new title.

If we find a duplicate, then we won't push the new note to the `notes` array, and the file will not be written. If there isn't a duplicate title, then we can safely update the array and write the file.

We can use a helper function `filter()` on the array of notes,

```js
// check for duplicate notes in the JSON file...
var duplicates = notes.filter((note) => note.title === title);
```

The `filter()` accepts a callback, including an arrow function, with an expected parameter of a single note. For each item in the `notes` array, this callback will be executed. With this helper function we have the opportunity to return a boolean of either true or false. We set it to `true` if the `note.title` is strict equals to the title for the new note.

If `true` is returned, then the note is added to the array of duplicates otherwise the duplicates array will remain empty for this execution.

Then, to check whether we need to save the new note or not, we simply check the length of the `duplicates` array. Obviously, if the length is 1 or more there is no need to save the note to the JSON file.

```js
// check length of duplicates before saving JSON file
if (duplicates.length === 0) {
    // add new note to existing notes array
    notes.push(note);
    // write note to notes JSON file
    fs.writeFileSync('notes.json', JSON.stringify(notes));
    //check note creation
    console.log("note created...");
} else {
    console.log("duplicate title found in notes...");
}
```

##### refactor code - part 1 - intro
Now introduce some basic refactoring of code, in particular the structure and usage of repeated functions for working with the notes.

In `notes_io.js` we have multiple instances where we need to fetch notes from the JSON file, and then save notes to this file.

##### refactor code - fetch and save
So, we can create two specific functions to allow us to fetch and save notes.

e.g.

```js
// fetch notes from JSON file
var fetchNotes = () => {
    ...
};
// save notes to JSON file
var saveNotes = (notes) => {
  ...
};
```

Then, we can move the `try-catch` blcok from `createNote()` to `fetchNotes()`, and return the notes from the `try` statement

```js
var fetchNotes = () => {
    // code to check for an error
    try {
        // read existing notes from JSON file
        var currentNotes = fs.readFileSync('notes.json');
        // parse notes string...
        return JSON.parse(currentNotes);
    } catch (error) {
        // if error, catch will be run...
        return [];
    }
}
```

This means the fetched *notes* will be returned when the function is called. We can also add a return for the `catch` statement, which will be an empty array.

In our `createNote()` function, we can also simplify the code by calling `fetchNotes()` instead of setting an empty array for notes, e.g.

```js
// fetch all notes from JSON
var notes = fetchNotes();
```

This means we'll either get an array full of notes returned or an empty array.

We can refactor our saving of notes as well by creating a new function `saveNotes()`. Then, we can move the file writing to this function, e.g.

```js
// save notes to JSON file
var saveNotes = (notes) => {
    // write note to notes JSON file
    fs.writeFileSync('notes.json', JSON.stringify(notes));
};
```

Then, when we need to save some notes we can call this function as follows, e.g. in the current `createNote()` function

```js
// save notes
saveNotes(notes);
```

##### refactor code - return notes
When we create a new note, we can also return details of this new note to the originating function.

For this app, we can return note details to the `createNote()` function in `app.js`.

In `saveNotes()` function, we can add a simply return for the note, e.g.

```js
// return note added - e.g. back to app.js
return note;
```

and then use this return in the `createNote()` function in `app.js`, e.g.

```js
...
var note = notesio.createNote(argv.title, argv.author);
if (note) {
  console.log("note created...");
  // return details of the created note
  console.log('Title: $(note.title)');
  console.log('Author: $(note.author)');
} else {
  console.log("note already saved...");
}
...
```

So, we're simply checking the return to inform the user about whether the note was created, or perhaps it was a duplicate.

##### delete notes - intro
We can also allow a user to delete a specified note, again by its `title` property.

##### delete notes - delete using filter()
Need to call a method, `deleteNote()`, e.g.

```js
// delete note by title from JSON
var deleteNote = (title) => {
    // fetch notes
    var notes = fetchNotes();
    // filter notes, remove note with title of passed argument
    var filterNotes = notes.filter((note) => note.title !== title);
    // save updated notes array - without deleted note...
    saveNotes(filterNotes);
};
```

Our initial `deleteNote()` function accepts the note title as a parameter, and then fetches all of the current notes using the abstracted function `fetchNotes()`.

Then, we can filter this return array of notes. We use the title of the note to check if it exists in the array of notes, and use the boolean option of the `filter()` helper function.

So, the function passed to `filter()` will return true, and add the note to the new `filterNotes` array, if the title does not match the title of the passed note. If a note's title does match, i.e. the one we want to remove, that note will not be added to the new filter array.

The filter array becomes our new array of notes, minus the deleted note.

##### delete notes - check if note deleted
Then, we can check whether a note has, indeed, been deleted from the notes array.

In effect, we can simply compare the lengths of the initial notes array against the updated filter notes array.

```js
// return check length of original and filtered notes arrays
return notes.length !== filterNotes.length;
```

This also allows us to output a confirmation to the console for the user - i.e. the requested note has or has not been deleted. This can be done by simply using the return boolean from the `deleteNotes()` function in the originating `app.js` file. e.g.

```js
// user ternary operator to check boolean return...
var deleteMessage = noteDelete ? "Note has now been deleted" : "Note has not been deleted - it could not be found...";
// output message to the console
console.log(deleteMessage);
```

We can use a simple ternary operator to check the value of the return boolean for the requested deleted operation. If the return value of `noteDelete` is `true`, then we can output a message to inform the user that the note has been deleted. If not, simply inform the user that the note has not been deleted.

##### read note - intro
We need to allow a user to read the body of a saved note. So, we need to fetch a single note, then output its details and content so the user can read the note.

##### read note - get a single note
As we get a single note from the notes JSON, we'll either have a single return note object or `undefined`.

We can use the standard `fetchNotes()` function, and then filter all notes for the title of the requested single note.

Then, we return the object for the single note. This can be output to the user with various details and comments.

e.g. we can update the `app.js` file to check for a user request to read a single note,

```js
if (cmd === 'read') {
    // read a single note
    var note = notesio.getNote(argv.title);
}
```

Then, we need to update the `getNote()` function in the `notes_io.js` file. We'll add the required logic, e.g.

```
// fetch notes
var notes = fetchNotes();
// filter notes, return note with title of passed argument - i.e. requested single note
var filterNotes = notes.filter((note) => note.title === title);
// return first item from filterNotes array - if empty, return will be `undefined`...
return filterNotes[0];
```

For the return note, we can also output a message informing the user if the note has been found &c., and any applicable details for the note itself, e.g.

```js
// read a single note
var note = notesio.getNote(argv.title);
if (note) {
    console.log("note found...");
    // return details of the found note
    console.log(`Title: ${note.title}`);
    console.log(`Author: ${note.author}`);
} else {
    console.log("requested note not found...");
}
```

We can then start to consider how best to refactor this code due to duplication of logic with other functions in the `app.js` file.

##### refactor code - part 2 - console statements for a note
We can refactor some of the code in `app.js` to avoid unnecessary duplication of logic. In particular, the output of note details to the console is being replicated in multiple functions.

In effect, DRY - don't repeat yourself...

We can create a single abstracted function for such output, which can then be called when needed in the app's code. e.g. simply move the repeated code to a separate function

```js
var consoleNote = (note) => {
    // return details of the requested note
    console.log(`Title: ${note.title}`);
    console.log(`Author: ${note.author}`);
};
```

##### debugging apps - intro
Node.js debugging is an important part of app development with JS.

There are two main options, which include the command line and Chrome Developer tools.

##### debugging apps - using the command line
From Node.js v8 onwards, debugging tools are available as part of the default install.

As we start an app with Node.js, we can add a call to the debugger tool as follows,

```
node inspect app.js add --title="test debugging..."
```

With this command, we are still calling the app with the required commands and arguments, but Node.js will also start a debugging session.

In this session, we can step through our app's code, execute statement by statement, and even interactively check and modify our app.

For example, whilst debugging is running for our app we can use the following commands,

```
debug> n // next - moves to the next statement in the app's execution to the end of the app
debug> c // continue - continues running the app from paused point to the end (from debug point to point)
debug> ctrl+c // use twice to close debugging
```

The first thing the debug option shows is how Node wraps each and every app in a function, which provides our standard options. These options include the default modules, e.g. FS, OS &c., exports, require, and so on.

##### debugging apps - using repl

REPL gives us an interactive mode whilst running our app in debug mode.

```
debug> repl
> note.title // interactive and test mode for the current running app
```

With REPL, we can interactively modify and test our app. e.g. output objects, modify a variable's value, and so on.

If we then add the following break to our app's code,

```
debugger
```

we can set pause points in our app. These can be used to step our way through the app in debug mode, e.g. by using `c` from point to point.

So, we have many ways to debug and test our app from the command line.

##### debugging apps - using Chrome Developer tools
To use Node.js debugging with Chrome Developer tools, we can issue a slightly modified node command to start our app,

```
node --inspect-brk app.js add --title="test debug note..."
```

This command informs node that we do not want to connect to the debugger directly using the command line.

When we run the app with this command, the debugger will start but it will connect in a CLI interative manner. Instead, we connect using the following URL with Chrome,

  * chrome://inspect

In Chrome, we should now see our app running under the `Devices` tab. Once this tab is open for the current app, there will be a link, usually titled `Open dedicated DevTools for Node`

A very useful tool for general debugging.

##### listing notes - intro
A simple addition to the existing app. We need to return all of the notes saved in the current JSON file or inform the user that there are no notes available.

##### listing notes - fetch all notes
We can add a simple function to `notes_io.js` to return all notes from the saved JSON file,

```js
// return all notes from JSON
var getNotes = () => {
    console.log("getting all notes...");
    // fetch all notes from current JSON file...
    return fetchNotes();
};
```

Then, in our `app.js` file, we can check for a user command to get all notes, `list`, and use the return array from `fetchNotes()`.

```js
// fetch all notes from JSON file
var listNotes = notesio.getNotes();
```

This array can be checked for length, which tells us how many notes are currently saved in the JSON file,

```js
console.log("Notes available = ", listNotes.length);
```

and, we can then iterate over the array to output each note using a simple `forEach` loop, e.g.

```js
// output each individual note in notes array - use abstracted consoleNote function...
listNotes.forEach((note) => notesio.consoleNote(note));
```

##### advanced yargs - intro
With advanced yargs configs, we can choose how to configure certain commands, their arguments, any requirements or defaults, and properties on arguments as well. For example, whether a given argument is actually or optional for the user.

Further info on yargs commands &c. can be found on the project's NPM page,

  * https://www.npmjs.com/package/yargs

Then, we'll see lots of available methods we can use with yargs under the section titled, *methods*.

For many instances, the main method we'll use for config of yargs will be `.command`. This can be used to help configure and setup how a command is used within an app, e.g. one of our app's commands such as `add` or `delete`.

##### advanced yargs - configuring yargs in app.js
In our `app.js` file, we can update yargs to specify some useful settings, e.g. for the `add` command

```js
.command('add', 'create a new note', {
    title: {
        describe: 'specify title of note',
        demand: true,
        alias: 'tl'
    }
})
.help()
```

So, we start by specifying the name of the command a user can enter on the CLI, then a brief description of the command itself, and finally an *options* object.

This *options* object allows us to specify any arguments this command may require. e.g.

  * describe - a brief description of the property &c.
  * demand - whether the property is required or not
  * alias - specify an alias for the property, instead of adding the flag `--title` for example

e.g. we might now call `add` as follows,

```
node app.js add -tl="a note title"
```

Then, we can simply update such settings for other properties of the command, e.g. author...

```js
...
.command(... {

})
.command(... {

})
.help()
...
```

##### advanced yargs - help the user

We can also chain a yargs `help()` method to the `command()` method. This will return lots of useful info to the user to help them understand a command and general app usage. This help resource may be called by the user with a simple flag, e.g.

```
node app.js --help
```

This may output helpful information such as available commands for the app, a brief description per command, and options available &c. We can also get just helpful information per command, e.g.

```
node app.js add --help
```

##### advanced yargs - refactor code
We can add basic refactoring of our yargs code to ensure we remove unnecessary DRY code.

e.g. we can move the options object for the title of a note to a separate object,

```js
// set options object for title usage in yargs
const yargsTitleOptions = {
    describe: 'note title',
    demand: true,
    alias: 'tl'
};
```

This variable can now be referenced whenever we need to add options for a note's title, e.g. `add`, `delete`, and `read` a single note.

Our updated yargs commands are as follows,

```js
// get arguments from command line - modify with yargs commands, help...
const argv = yargs.command('add', 'create a new note', {
    title: yargsTitleOptions,
    author: {
        describe: 'author\'s name',
        demand: true,
        alias: 'aut'
    }
})
.command('list', 'list all saved notes')
.help()
.argv;
```

We can abstract any repeated code to help reduce unnecessary repetition within our app's code.
