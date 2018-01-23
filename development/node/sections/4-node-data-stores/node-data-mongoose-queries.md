### Node.js - Guide
#### section 4 - Data Stores - Mongoose query tests

A brief outline of Mongoose queries and tests for use with Node.js based development.

#### Contents
* intro
* Mongoose query tests
* Mongoose validation checking

#### app - Mongoose query tests
There are many different options with Mongoose for querying data, more than simply `find`.

So, we can add a new directory for testing these queries, `mongoose-tests`, and then add a file for these queries, `mongoose-queries.js`.

We'll start by requiring the mongoose config file for connecting to MongoDB, and the model for a Todo. We'll also need an ObjectID from a document in the `todos` collection, which we can use for testing, e.g.

```js
// require mongoose config
const {mongoose} = require('./../server/dbms/mongoose-config.js');
// require models - todo
const {Todo} = require('./../server/models/todo-model.js');

// specify test doc ID
var docID = '5979f0f0f3a968291ae5bf63'; // ObjectID for doc from collection
```

**n.b.** Mongoose is happy with strings as IDs, and they do not need to be converted as with MongoDB native driver.

We can then add some queries.

```js
// find
Todo.find({
  _id: docID // mongoose will convert this string to an ObjectID
}).then((todos) => { // returns array of todo items
  console.log('all todo items - ', todos);
});

// find one - returns first match
Todo.findOne({
  completed: false
}).then((todo) => { // returns single item for first match
  console.log('single todo item - ', todo);
});
```

The first query will find all docs that match the specified ObjectID, whilst the second query will return the first document that matched the specified query.

If we're searching for a single document, the `findOne()` query may be preferable as it returns an object. The standard `find()` query returns an array containing the found documents. For a single document, this will still be an array with the required object for the document. The other benefit is that `findOne()` will return `null` for an empty query. `find()` will still return an array, but it will be empty for no result found.

We might also need to find by a specific, known ObjectID, e.g.

```js
Todo.findById(docID).then((todo) => {
  console.log('single todo item by ID - ', todo);
});
```

#### app - Mongoose validation checking
We can start cursory testing of false, missing or error-prone ObjectIDs, and then use validation with Mongoose to handle such errors &c.

So, an empty result set will return an empty array for `find()`, and `null` for both `findOne()` and `findById()`. This means that a false or incorrect ObjectID will still return a result for the query. It will not handle the false or incorrect ObjectID as an error, but simply an empty return result.

We can, therefore, check for an empty result set for the query, e.g.

```js
Todo.findById(id).then((todo) => {
  if (!todo) { // check for null return
    return console.log('Object ID not found...');
  }
  console.log('Todo ID not found - ', todo);
});
```

If we had an error where the ObjectID was invalid, perhaps there were too many numbers to the ID, we could add a `catch` method to `findById()`. This would catch the error that the ID is not in the specified collection, and that the ID itself is not a valid format. e.g.

```js
Todo.findById(docID).then((todo) => {
  if (!todo) { // check for null return for query...
    return console.log("specified ID has not been found...");
  }
  console.log('single todo item by ID - ', todo);
}).catch((error) => console.log(error, 'Specified ID is not valid - try another one...')); // catch validation error with ID error &c. i.e. ID is invalid - perhaps too long...
```

We might then use this to output a response to the user informing them that the entered ID &c. is not valid.

Another option for validation is to integrate MongoDB native driver with Mongoose. We can use a method called `isValid()` relative to the ObjectID.

We can start by loading ObjectID from the MongoDB native driver,

```js
const {ObjectID} = require('mongodb');
```

We can then use the method `isValid()` relative to ObjectID. e.g.

```js
if (!ObjectID.isValid(docID)) {
  // execute if ID not valid
  console.log('ID not valid...');
}
```
