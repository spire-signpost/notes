### Node.js - Guide
#### section 6 - API - TODOS app

A brief outline of a basic API for a TODOS app with Node.js &c.

#### Contents
* intro
* Mongoose - setup
* initial app setup
* Mongoose - create a model
* Mongoose - use a model
* Mongoose - use validators, types, and defaults
* testing - API testing with Postman
* refactor code for mongoose and models
* build a basic API - setup server
* build a basic API - POST route
* testing - test POST route with Postman
* testing - API route - POST
* build a basic API - GET route
* testing - API route - GET
* Mongoose - query tests
* Mongoose - validation checking
* build a basic API - GET route with parameter
* Heroku - deploy app with MongoDB publication
* testing - use Postman with Heroku app
* build a basic API - DELETE route with parameter
* testing - API route - DELETE
* build a basic API - PATCH route with parameter
* testing - API route - PATCH
* testing - create a separate database for tests
* basic refactor of code structure
* CRUD API for todos app

#### intro
Combine Node.js, Mongoose, and MongoDB with a UI client-side to create a Todos app.

#### initialise basic app
We can start by initialising a basic Node.js based app,

```bash
npm init
```

This will create an initial `package.json` file with metadata for the current project.

#### app - Mongoose - setup
Mongoose helps create schema-based models for an application's data. e.g. define a schema for a user document &c.

It also helps with type casting, validation, structuring queries to MongoDB, and so on. Further details,

  * mongoosejs.com/docs/guide.html

Install Mongoose,

```bash
npm i mongoose --save
```

#### app - initial app setup
Add a folder for the `server` to the root of the app, and then add a new file `server.js`.

This file will, effectively, act as the starter, a type of bootstrapping, for the app.

So, we can require Mongoose and connect to the DB in MongoDB. We can also specify a Promise library for use with Mongoose. In this example, we're going to use the default, built-in Promises with ES6, e.g.

```javascript
const mongoose = require('mongoose');

mongoose.Promise = global.Promise;
mongoose.connect('mongodb://localhost:27017/NodeTodoApp');
```

#### app - Mongoose - create a model
Create a Mongoose model for data we want to store in MongoDB.

With Mongoose, we can specify a name for the model, and various properties we require for each document created from this model. This might include a . e.g.

```javascript
// specify model for Todo item
var Todo = mongoose.model('Todo', {
  // specify requirements for a property of a todo item
  text: {
    type: String
  },
  completed: {
    type: Boolean
  },
  completedAt: {
    type: Number
  }
});
```

#### app - Mongoose - use a model
We can now use our defined model to create documents we can store in MongoDB.

We use our model as a constructor to create a new document object, which we can then save to the connected DB. Each model requires the specified properties as well, e.g.

```javascript
// create a new Todo item from the model
var todoItem = new Todo({
  text: "walk the Great Wall of China"
});

// save the object as a document in the DB - save returns a promise
todoItem.save().then((doc) => {
  console.log('todo item saved', doc);
}, (error) => {
  console.log('todo item not saved: ', error);
});
```

#### app - Mongoose - use validators, types, and defaults
Mongoose helps us specify default values for properties in models, check types &c., and easily validate data before it is saved to MongoDB.

Mongoose supports many built-in validators for booleans, numbers (min and max), strings (match, min and max length &c.) &c.

Further details at,

  * mongoosejs.com/docs/validation.html

So, we might add a validator for properties in a model, e.g.

```javascript
// specify model for Todo item
var Todo = mongoose.model('Todo', {
  // specify requirements for a property of a todo item
  text: {
    type: String,
    required: true, // text property is required to create a new document
    minlength: 1 // todo item must have at least 1 character
    trim: true // removes leading and trailing spaces
  }
  ...
});
```

These validators allow us to customise the text property for a todo item before it is saved to the DB. This property is set as required, has minimum character length of 1 to avoid empty text, and has all leading and trailing white space removed from the string. It will then validate successfully, and can be saved to the DB.

We can also add validators for other properties in a model. e.g. a `default` value for a given property

```javascript
...
completed: {
  type: Boolean,
  default: false // specify default value
}
...
```

#### app - testing - API testing with Postman
A very useful, and commonly used, tool to help develop and test REST APIs. App URL available,

  * https://www.getpostman.com

Apps available for MacOS (OS X), Windows, and Linux. Download package for required OS, and follow usual install instructions.

You can also install an app for Chrome.

To use Postamn either open the desktop app or Chrome app,

  * chrome://apps  (then select Postman app)

and start with the *Builder* tab in the app window.

We can start with a `GET` HTTP method, and then enter a test URL for the request. e.g.

  * https://maps.googleapis.com/maps/api/geocode/json?address=3%20the%20strand%20brixham

This will return some JSON, and there is usually tabs available for *Body*, *Cookies*, *Headers*, and *Tests*. There will also be some extra sub-tabs for *Pretty*, *Raw*, and *Preview*.

As we build out a custom API, we can then test it using Postman.

#### app - refactor code for mongoose and models

We can organise our app's code into logical groupings, including a new directory for datastore/base management files and setup. e.g. `server/dbms/moongoose-config.js`. This should include config and connection settings using Mongoose for the specified MongoDB.

This may then be required in the app's main server.js file. e.g.

```javascript
// get mongoose property using ES6 destructuring - name of created local variable will match the property of the object
var {mongoose} = require('./dbms/mongoose-config.js');
```

We can then do the same for other logic in the `server.js` file not required to setup and run the Express server. e.g. models, queries, &c.

So, we can now create some separate models for the app, including `todo-model.js` and `user-model.js`. These modules can then be required in `server.js`, e.g.

```javascript
var {Todo} = require('./models/todo');
```

Using the new ES6 destructuring, we can create a new variable with the name of the property on the object returned from loading the specified custom module file. So, we have a variable called `Todo`, which is the property Todo of the object returned from the local file.

This is the whole point of the `module.exports` for the custom file, e.g.

```javascript
// module export
module.exports = {
  Todo // ES6 shortcut for Todo: Todo
};
```

An object is returned for the `exports`, which contains a property of `Todo`. `Todo` references the variable Todo for the model in the custom file.

#### app - build a basic API - setup server
So, we can now start to build out a basic API for the app. For example, we might add routes for POST and GET, which will allow an app to query data, and then modify or add data.

We'll start by adding the required Express to the current app,

```bash
npm install express --save
```

and a module called `body-parser`, which allows us to send JSON to the server. In effect, `body-parser` takes the string body and parses it into a JS object.

```bash
npm install body-parser --save
```

These modules will then be required in the `server.js` file, and we can add the basic code for a server, e.g.

```javascript
...
// create express app
var app = express();

// set port for server
app.listen(3030, () => {
  console.log('server started on port 3030...');
});
```

#### app - build a basic API - POST route
We can now add a POST route to allow our app to save data, effectively by posting that data to the specified data store.

For a POST route, e.g.

```javascript
app.post('/todos', (req, res) => {

});
```

we need to configure the middleware for the `body-parser`, e.g.

```javascript
app.use(bodyParser.json());
```

So, the body-parser module will parse the body into a JS object, which is then attached to the `req` object for the route. We can also check this relative to the POST route, for example

```javascript
app.post('/todos', (req, res) => {
  console.log(req.body); // log body parsed by body-parser for the req object...
});
```

Then, we can create a new Todo item using the Todo model, e.g.

```javascript
app.post('/todos', (req, res) => {
  // create todo item from model
  var todo = new Todo({
    text: req.body.text // specify text for each todo item
  });

  todo.save().then((doc) => {
    res.send(doc); // send back to the saved document details
  }, (error) => {
    // send back errors...
    res.status(400).send(error); // send back error and status code for request...
  })
});
```

#### app - testing - test POST route with Postman
We can now start the server, and then test this new route with Postman.

In Postman, change the HTTP method to POST, and then enter the url for the created API route, e.g.

  * localhost:3030/todos

Then, select the Body tab in Postman, and add a sample todo object, e.g.

```javascript
{
  "text": "find the source of the Nile..."
}
```

Then, hit Send to test the POST route for the app. If successful, the console should log the saved object.

Also, if the POST route is working correctly a send response will be returned for the Todo data, along with a status code. For success, Postman will show a `200` status code.

By modifying the body of the test object for the POST route in Postman, we can test errors, model defaults &c. e.g. if we removed the content of the `text` for a todo, the response will show errors with a status code of 200.

#### app - testing - API route - POST
For broader testing of our app, and its functions, we can oncemore use

  * Expect - for assertions
  * Mocha - overall test suite
  * Supertest - test Express route
  * Nodemon - helps with the test watch script

So, we need to install them for our app,

```bash
npm i expect mocha supertest nodemon --save-dev
```

Then create a new folder in the `server` directory for these tests, e.g. `/server/tests` with a test file for the server, `server.test.js`.

In this `server.test.js` file, we can then add the require modules and local scripts for the tests, e.g.

```javascript
// require node modules
const expect = require('expect');
const request = require('request');
// require local files
const {app} = require('./../server.js');
const {todo} = require('./../models/todo-model.js')
```

Then we can add a `describe` block for the `POST` API routes,

```javascript
// describe for the POST API route
describe('POST /todos', () => {
  // add test cases for this route
  it('should create a new todo item', (done) => {
    var text = 'some text for a todo item...';

    // use Supertest to test POST
    request(app)
      .post('/todos')
      .send({
        text // ES6 shortcut for text: text
      })
      .expect(200) // assertion to test status code
      .expect((res) => { // create custom assertion to test response body text
        expect(res.body.text).toBe(text); // test that the response text matches the text specified above in var text
      })
      .end((error, res) => { // check todo item was saved to MongoDB
        if (error) {
          return done(err);
        }
        Todo.find().then((todos) => {
          expect(todos.length).toBe(1);
          expect(todos[0].text).toBe(text);
          done();
        }).catch((e) => done(e)); // catch any errors in callback - then pass to done() to finish
      });

  });
});
```

In this test example, we're adding a number of assertions to test the response from the POST API route. We start by testing the text response and status code, then add a check for the end of the POST request to ensure that the Todo item is saved correctly to MongoDB. Finally, we add a catch statement to check for errors in the callback, which are then passed to `done()` to finish the execution of this describe block.

However, when we check the data store for Todo items, we're assuming that there is always `0` items saved before we test this new Todo item.

For testing purposes, we can overcome this issues by simply wiping the data store before any test code is executed, e.g.

```javascript
// before a describe block is executed - wipe existing todo items stored in data store
beforeEach((done) => {
  Todo.remove({}).then(() => done())
});
```

In our app's `package.json` file, we can now update the `"scripts"` value by adding a `test-watch` option for Nodemon. e.g.

```javascript
"test": "mocha server/**/*.test.js",
"test-watch": "nodemon --exec 'npm test'"
```

and then run the test using the following command,

```bash
npm run test-watch
```

An extra test is to check that a todo item is not created with invalid data, e.g.

```javascript
it('should not create a todo item with invalid data', (done) => {
  request(app)
    .post('/todos')
    .send({}) // send empty data to post route
    .expect(400) // expect status code 400
    .end((error, res) => {
      if (error) {
        return done(error); // finish test if error returned
      }

      // find and return all todos in the DB - DB wiperd beforeEach - should be 0 if no todo item created...
      Todo.find().then((todos) => {
        expect(todos.length).toBe(0);
        done();
      }).catch((e) => done(e));
    });
});
```

If we now run the same test, we should get both test passing. One for adding a todo item to the DB, and the other for invalid data not being saved to the DB. e.g. output

```bash
...
  POST /todos
    ✓ should create a new todo item (71ms)
    ✓ should not create a todo item with invalid data

  2 passing (118ms)

[nodemon] clean exit - waiting for changes before restart
```

#### app - build a basic API - GET route
We can now add a GET route to find and return all todo items in the DB.

It follows a similar pattern to the previoud POST route,

```javascript
// GET route for todo items
app.get('/todos', (req, res) => {
  Todo.find().then((todos) => { // promised resolved with all of the todos from the db
    res.send({ //response - send data back from get route - all of the todos
      todos // add todos array to object - update and modify object as needed instead of just sending array response...
    });
  }, (error) => { // error callback if error with promise
    res.status(400).send(error); // send back error and status code for request...
  });
});
```

We can then start the server,

```bash
node server/server.js
```

and then test the GET route in Postman, again using the local route of

  * localhost:3030/todos

with a GET HTTP method for the request. Then press send and check the return from the `todos` route.

#### app - testing - API route - GET
To test GET routes, we'll need to ensure that we have some data in the DB to query. In tests for the POST route, we clear the DB on each pass of the test, so we need to modify this `beforeEach()` function to seed some known dummy data. e.g.

```javascript
// create some dummy data - test todo items
const todos = [
  {text: 'a todo item...'},
  {text: 'another todo item...'}
];
```

Then, we need to update the `beforeEach()` function in the `server.test.js` file. As we're adding multiple objects at the same time, we can now use the new MongoDB function `insertMany`, e.g.

```javascript
beforeEach((done) => {
  Todo.remove({}).then(() => {
    return Todo.insertMany(todos);
  }).then(() => done());
});
```

We'll also need to modify any checks with the length property to match these dummy data objects.

We can add further testing for the GET route with a test case,

```javascript
// describe for the GET API route
describe('GET /todos', () => {
  it('should GET all todo items...dummy data found', (done) => {
    request(app)
      .get('/todos') // specify api url
      .expect(200) // check status code - 200 for OK
      .expect((res) => { // custom assertion
        expect(res.body.todos.length).toBe(2);
      })
      .end(done);
  });
});
```

and then run our tests from the terminal,

```bash
npm run test-watch
```

#### app - Mongoose - query tests
There are many different options with Mongoose for querying data, more than simply `find`.

So, we can add a new directory for testing these queries, `mongoose-tests`, and then add a file for these queries, `mongoose-queries.js`.

We'll start by requiring the mongoose config file for connecting to MongoDB, and the model for a Todo. We'll also need an ObjectID from a document in the `todos` collection, which we can use for testing, e.g.

```javascript
// require mongoose config
const {mongoose} = require('./../server/dbms/mongoose-config.js');
// require models - todo
const {Todo} = require('./../server/models/todo-model.js');

// specify test doc ID
var docID = '5979f0f0f3a968291ae5bf63'; // ObjectID for doc from collection
```

**n.b.** Mongoose is happy with strings as IDs, and they do not need to be converted as with MongoDB native driver.

We can then add some queries.

```javascript
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

```javascript
Todo.findById(docID).then((todo) => {
  console.log('single todo item by ID - ', todo);
});
```

#### app - Mongoose - validation checking
We can start cursory testing of false, missing or error-prone ObjectIDs, and then use validation with Mongoose to handle such errors &c.

So, an empty result set will return an empty array for `find()`, and `null` for both `findOne()` and `findById()`. This means that a false or incorrect ObjectID will still return a result for the query. It will not handle the false or incorrect ObjectID as an error, but simply an empty return result.

We can, therefore, check for an empty result set for the query, e.g.

```javascript
Todo.findById(id).then((todo) => {
  if (!todo) { // check for null return
    return console.log('Object ID not found...');
  }
  console.log('Todo ID not found - ', todo);
});
```

If we had an error where the ObjectID was invalid, perhaps there were too many numbers to the ID, we could add a `catch` method to `findById()`. This would catch the error that the ID is not in the specified collection, and that the ID itself is not a valid format. e.g.

```javascript
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

```javascript
const {ObjectID} = require('mongodb');
```

We can then use the method `isValid()` relative to ObjectID. e.g.

```javascript
if (!ObjectID.isValid(docID)) {
  // execute if ID not valid
  console.log('ID not valid...');
}
```

#### app - build a basic API - GET route with parameter
We can create an API route to allow a user to request a route with an additional parameter for the URL, e.g.

  * localhost:3030/todos/4321

The parameter, `4321`, will then be available as a property of the request object, as specified below

```javascript
// GET route with parameter
app.get('/todos/:id', (req, res) => {
  // get params from req
  var params = req.params;
  console.log(params);
});
```

The passed parameter in the URL is specified as `:id` in the GET function above. So, the above would return an object with a name value pair,

```javascripton
{
  "id": "4321"
}
```

For this type of route with user input, we'll need to add some additional validation, error handling, and returns.

So, we'll add the `ObjectID` property from the MongoDB object (mongoose native driver),

```javascript
var {ObjectID} = require('mongodb');
```

and then check if the passed ID parameter is valid, e.g.

```javascript
// validate passed ID - check not valid
if (!ObjectID.isValid(params_id)) {
  // return 404 status code for invalid ID
  return res.status(404).send();
}
```

We could then use Mongoose `findById()` to get the returned data for the ID parameter. e.g.

```javascript
Todo.findById(params_id).then((todo) => {
  // check if return data available
  if (!todo) {
    return res.status(404).send();
  }

  // otherwise return the data for the params ID
  res.send({todo}); // return todo in object - more flexible than default array return
}).catch((error) => { // catch return errors for query
  res.status(400).send();
})
```

So, when we call the `/todos/:id` route, we'll now get either a successful return for the query, or an error message.

#### app - Heroku - deploy app with MongoDB publication
We can now modify our app for test publication to Heroku with a live instance of MongoDB.

Config modifications for current app code include,

`server.js`
  * add environment port variable - Heroku can dynamically set port number of app or app will default to local 3030
  * `app.listen()` - updated to use variable `port` for dynamic port setting

`package.json`
  * add `start` to scripts - tells Heroku how to run our app
    * `"start": 'node server/server.js'`
  * specify node version to use for hosted app
    * add `engines` with current version used for developing app on local system
    * e.g. `"engines": {"node": "8.2.1"}`

Then, we need to create a remote instance of MongoDB for our app's data. On Heroku, we install an add-on for *mLab MongoDB*, which is a hosted service for MongoDB.

Further details on Heroku add-ons,

  * https://elements.heroku.com/addons

To install this add-on we can run the following command in our project's CWD,

```
heroku create //create a new heroku app if necessary
heroku addons:create mongolab:sandbox //install add-on for mLab MongoDB with free sandbox plan
```

mLab offers a free sandbox option, which is installed for our app.

Then, for connections to MongoDB, we can modify `mongoose-config.js` to add a dynamic URL for the db. This will either include the return URL for mLab or the current default for the local machine, e.g.

```javascript
//connect to MongoDB using Mongoose - use mLab or local uri
mongoose.connect(process.env.MONGODB_URI || 'mongodb://localhost:27017/NodeTodoApp'); // process environment returns mLab uri
```

For some apps and Git repos, it may also be necessary to ensure that the local project is linked to the remote app repo on Heroku, e.g.

```bash
heroku git:remote -a app-name
```

Then, we can push our project repo to Heroku, and deploy the app, e.g.

```bash
git push heroku master
```

or for a subtree directory in a git repo, e.g.

```bash
git subtree push --prefix node/apps/node-todos-api/v0.8 heroku master
```

We can then open the heroku app with the following command, e.g.

```bash
heroku open
```

As a useful extra, we can also check the logs for this type of install, e.g.

```bash
heroku logs
```

#### app - testing - use Postman with Heroku app
We can now test our new Heroku app with Postman, both GET and POST requests for the new remote app.

We might test sending a POST request to the app, e.g.

  * https://your-app.herokuapp.com/todos

which will create a test todo item that is set in the app. The return object for this POST request will be as follows,

```javascript
{
    "__v": 0,
    "text": "postman test todo item - another one...",
    "_id": "597cd962d828090011f2b9ce",
    "completedAt": null,
    "completed": false
}
```

If we then submit a GET request to the app's API,

  * https://your-app.herokuapp.com/todos

we'll get the expected object containing an array of todo items, e.g.

```javascript
{
    "todos": [
        {
            "_id": "597cd962d828090011f2b9ce",
            "text": "postman test todo item - another one...",
            "__v": 0,
            "completedAt": null,
            "completed": false
        }
    ]
}
```

We can test retrieving a single todo item by ID, e.g.

  * https://your-app.herokuapp.com/todos/597cd962d828090011f2b9ce

which will return an object with the single todo item,

```javascript
{
    "todo": {
        "_id": "597cd962d828090011f2b9ce",
        "text": "postman test todo item - another one...",
        "__v": 0,
        "completedAt": null,
        "completed": false
    }
}
```

To ease switching test environments in Postman, we can create environments for local, Heroku &c. and then save them for easy recall.

e.g. in the top right corner of Postman is a drop down menu for environemnt.

So, we can now create an environment for the local dev and remote dev projects.

In *Manage Environments*, we can add an environment, e.g. `Todo App Local`, and then set values for the following

  * url = localhost:3030

Then, we can do the same for Heroku, and set the URL value to the Heroku app url, e.g.

  * https://your-app.herokuapp.com

We can also abstract routes and params as required for testing with defined environments.

e.g. for the GET request in the *Todo App* collection we can modify the URL as follows,

  * {{url}}/todos

If we switch to either the local or Heroku environment, this single request is now abstracted to either environment.

We can also do the same for the POST request in the collection.

#### app - build a basic API - DELETE route with parameter
We can now add a route to remove/delete a todo item document by specified ID parameter.

Mongoose helps us by providing three methods for removing/deleting documents from MongoDB.

So, we might need to remove all docs from the DB, e.g.

```javascript
// remove all docs from DB
Todo.remove({}).then((result) => { // empty object required to delete all docs
  consol.log(result);
});
```

Or perhaps find a doc by ID and then delete it, e.g.

```javascript
// find a single doc by ID and remove...
Todo.findByIdAndRemove('597b92e6031086379d868696').then((todo) => {
  console.log(todo);
});
```

We can also find a document by another property of the saved doc, e.g.

```javascript
// find a single doc and remove from db - single doc removed will also be returned
Todo.findOneAndRemove({completed: true}).then((todo) => { // useful to find doc without ID - i.e. by text, author &c.
  console.log(todo);
});
```

This will still only delete a single doc, the first found in the DB.

In `server.js`, we can the create our route to expose the delete options for our API.

```javascript
// DELETE route for single doc with ID
app.delete('/todos/:id', (req, res) => {
  // get params ID from req
  var params_id = req.params.id;
  console.log(params_id);

  // validate passed ID - check not valid
  if (!ObjectID.isValid(params_id)) {
    // return 404 status code for invalid ID
    return res.status(404).send();
  }

  // find doc by ID and remove from DB
  Todo.findByIdAndRemove(params_id).then((todo) => {
    // check if return data available
    if (!todo) {
      return res.status(404).send();
    }
    // otherwise return the data for the deleted params ID
    res.send(todo);
  }).catch((error) => { // catch return errors for the query
    res.status(400).send();
  });
});
```

The pattern used for the route is very similar to finding a single todo by ID. We simply use a different method with the requested ID to remove the doc from the DB.

We can then test this new route with Postman, creating a DELETE request for the server. We need to select the DELETE http method from the drop down method, and then specify the following sample route for a single doc id,

  * {{url}}/todos/597b92e6031086379d868695

This will now search for this doc ID in MongoDB, and remove it if found. The status return for this query should be `200`, and the deleted doc will be returned in the response, e.g.

```javascript
{
    "_id": "597b92e6031086379d868695",
    "__v": 0,
    "text": "a todo item...",
    "completedAt": null,
    "completed": true
}
```

Then, if we run our GET route for all todo docs, we should see the doc has now been removed from the collection in the DB.

#### app - testing - API route - DELETE
We need to add some test cases for the DELETE route, e.g.

```javascript
// check DELETE route with params ID
describe('DELETE /todos/:id', () => {
  // test case - check requested todo doc item has been removed
  it('should delete a doc for a todo item', (done) => {

  });

  // test case - check return status code for doc not found in DB
  it('should return a 404 status code for doc not found', (done) => {

  });

  // test case - check if doc object id is valid
  it('should return a 404 status code for invalid ObjectID...', (done) => {

  });

});
```

For the first test case, we need to send the query to the DB, check the response, and then check the DB itself to ensure that the doc was actually deleted successfully.

We can then fill out the first test case as follows,

```javascript
// test case - check requested todo doc item has been removed
it('should delete a doc for a todo item', (done) => {
  // specify test todo to delete
  var hexId = todos[1]._id.toHexString();

  request(app)
    .delete(`/todos/${hexId}`) // remove the specified doc by id
    .expect(200) // assert a 200 status code for the successful doc deletion
    .expect((res) => { // add custom assertion
      expect(res.body.todo._id).toBe(hexId); // assert that response body todo doc id matches the hexId
    })
    .end((error, res) => { // finish request
      if (error) { // handle error
        return done(error); // if error exists simply return the request as done...
      }

      // find doc id in db
      Todo.findById(hexId). then((todo) => {
        expect(todo).toNotExist(); // check that doc id does not exist in db
        done(); // call done and finish async all
      }).catch((error) => done(error)); // catch any error for async call - return done if error caught...
    });
});
```

We can then run our tests as usual with the following command,

```bash
npm run test-watch
```

We can then update the remaining two tests for the DELETE route,

```javascript
// test case - check return status code for doc not found in DB
it('should return a 404 status code for doc not found', (done) => {
  var hexID = new ObjectID().toHexString();

  request(app)
    .delete(`/todos/${hexID}`) // DELETE route to test with param ID
    .expect(404) // assert - status code should be 404
    .end(done); // call end and pass done to finish test case
});

// test case - check if doc object id is valid
it('should return a 404 status code for invalid ObjectID...', (done) => {
  request(app)
    .delete('/todos/abc123def') // pass in test invalid string - ObjectID has v. specific pattern
    .expect(404)
    .end(done);
});
```

The final two test cases follow the same pattern as testing for a GET route. We simply update the route to match the required DELETE route.

#### app - build a basic API - PATCH route with parameter
To help with adding and configuring the update PATCH route, we can oncemore use the JS utility library *Lodash*.

```bash
npm install lodash --save
```

and then require this library in the `server.js` file,

```javascript
const _ = require('lodash');
```

We can then setup the PATCH route itself for *todos* with the ID params, e.g.

```javascript
// PATCH route for single doc with ID
app.patch('/todos/:id', (req, res) => {

});
```

We'll then add some necessary variables, e.g.

```javascript
// get params ID from req
var params_id = req.params.id;
console.log(params_id);
// only pick the properties we need for an update - stops false, unnecessary &c. properties being sent by the user
var body = _.pick(req.body, ['text', 'completed']); // pick method from lodash - gets only specified properties from return req
```

As before, we'll get the required doc ID from the route params. Then, we'll create a `body` variable for the data to update. We can restrict the properties we need for the doc update by only *picking* the necessary properties from the `req` object.

As we've done for other routes with an ID param, we can check if the requested ID is valid or not,

```javascript
// validate passed ID - check not valid
if (!ObjectID.isValid(params_id)) {
  // return 404 status code for invalid ID
  return res.status(404).send();
}
```

Then, we need to check the `completed` state of the todo item doc, e.g.

```javascript
// check boolean return for completed i.e. true - reflects simple toggle update from false to true...
if (_.isBoolean(body.completed) && body.completed) {
  // app uses boolean = true as reason to set completedAt to current Unix timestamp - not set by user
  body.completedAt = new Date().getTime(); // returns no. of ms from midnight 1 jan 1970 to current date...
} else {
  // keep doc completed as false
  body.completed = false;
  // keep completedAt as not set - null
  body.completedAt = null;
}
```

So, we can check that the return `completed` property is, indeed, a boolean, and then check its value. A simple conditional statement is then used to set the `completedAt` time in milliseconds, or persist the todo item doc as not yet completed.

Finally, we can update the requested doc in the DB, e.g.

```javascript
// update the requested doc in the db - using Mongoose method, findByIdAndUpdate()
Todo.findByIdAndUpdate(params_id, {$set: body}, {new: true}).then((todo) => { // MongoDB update - set object to body, and return the new doc object - new: true (mongoose naming for returnOriginal)
  // check todo object exists - return 404 for not found
  if (!todo) {
    return res.status(404).send();
  }
  // if todo found - send todo object
  res.send({todo});
}).catch((error) => { // catch error
  res.status(400).send(); // send back error status - bad request code
});
```

We can use a Mongoose method, `findByIdAndUpdate()`, passing the doc id, and some options for the update. The first option is the MongoDB option to `$set` some data for the doc. In this example, we can simply return the `body` object, and set it as the updated data for this doc. Then, we can tell Mongoose to return the update doc object. This might be useful to check and inform the user of the successful update.

In the `then()` method, we can check that the todo object exists, and handle any errors. If no errors, we can simply send the todo object.

We can now test this PATCH route with Postman, creating a test request for the PATCH http method, and saving it to the todos app collection in Postman. A sample return object for a successfully updated doc with PATCH is as follows,

```javascript
{
    "todo": {
        "_id": "597df45fbc86a956132315fb",
        "__v": 0,
        "text": "another todo item...",
        "completedAt": 1501442182468,
        "completed": true
    }
}
```

This return is in response to simply updating the `completed` property to `true`.

#### app - testing - API route - PATCH
To be able to test PATCH routes for todo items, we'll need to update the dummy todo objects to include a `completed` and `completedAT` property, e.g.

```javascript
// update dummy todo items with test ID name:value pair property
const todos = [
  {
    _id: new ObjectID(),
    text: 'a todo item...'
  },
  {
    _id: new ObjectID(),
    text: 'another todo item...',
    completed: true,
    completedAt: 230797
  }
];
```

Then, we need to add some test cases for the PATCH route, e.g.

```javascript
// check PATCH route with params ID
describe('PATCH /todos/:id', () => {
  // test case - check update with params for doc id
  it('should patch and update the todo item', (done) => {

  });

  // test case - check completedAt relative to complete property
  it('should reset and clear completedAt property when todo item is not completed', (done) => {

  });

});
```

For the first test case, we can add the following requests and assertions to test this route, e.g.

```javascript
// test case - check update with params for doc id
it('should patch and update the todo item', (done) => {
  // get ID from dummy todos object - first object
  var hexId = todos[0]._id.toHexString();
  // text for testing PATCH update
  var text = 'some test new text...';

  // setup test with assertions
  request(app)
    .patch(`/todos/${hexId}`)
    .send({
      completed: true,
      text // ES6 shortcute for name:value pair
    })
    .expect(200)
    .expect((res) => {
      expect(res.body.todo.text).toBe(text);
      expect(res.body.todo.completed).toBe(true);
      expect(res.body.todo.completedAt).toBeA('number');
    })
    .end(done);
});
```

With this first test case, we're updating the todo item with text, and then setting the `completed` property to true. We can then check the response with assertions for the new text, `completed` property set to true, and the updated `completedAt` property to a number for the time in ms.

The second test case can then follow a similar pattern, e.g.

```javascript
// test case - check completedAt relative to complete property
it('should reset and clear completedAt property when todo item is not completed', (done) => {
  // get ID from dummy todos object - second object
  var hexId = todos[1]._id.toHexString();
  // text for testing PATCH update
  var text = 'some more test new text...';

  // setup test with assertions
  request(app)
    .patch(`/todos/${hexId}`)
    .send({
      completed: false, // todo item not completed
      text // ES6 shortcute for name:value pair
    })
    .expect(200)
    .expect((res) => {
      expect(res.body.todo.text).toBe(text); // assert text matches dummy text
      expect(res.body.todo.completed).toBe(false); //assert completed property false - todo item not completed
      expect(res.body.todo.completedAt).toNotExist(); // assert completedAt does not exist - should be null...
    })
    .end(done);
});
```

In this example, we're now getting the id for the second test todo item object, and then sending some new test text, and setting the `completed` property to false. This means we need to slightly modify the assertions to check for `completed` property as false, and that the `completedAt` property does not exist. There is no number, just a value set to `null` for this property.

#### app - testing - create a separate database for tests
As we run these tests, we wipe the local DB to ensure we're running the test cases with known data &c.

However, for local development this can cause issues and is annoying for ongoing development.

Instead, we can simply create and specify a separate local DB for testing these test cases. We'll also need to update our app to recognise the different process environments, including remote (e.g. Heroku), local development, and local testing with Mocha.

The Express module popularised an environment variable for an app's process, e.g.

```javascript
process.env.NODE_ENV === 'production' // value set by Heroku for live app
```

We might also set the value of this environment variable to `testing` or `development` for local development purposes.

We can also add a new variable to the `server.js` file for the current process environment for the app or simply a default environment, e.g.

```javascript
var env = process.env.NODE_ENV || 'development';
```

Then, we need to configure the available `NODE_ENV` values in `package.json`, e.g.

```javascripton
"test": "export NODE_ENV=test || SET \"NODE_ENV=test\" && mocha server/**/*.test.js"
```

This will set the environment to testing for any test files we run for this app. The first option, using `export`, is compatible with OS X and Linux, whilst Windows uses the `SET` command.

With the environment variable now set to either development, testing or production, we can now check its value in the `server.js` file with a standard conditional statement, e.g.

```javascript
if (env === 'development') {
  process.env.PORT = 3030;
  process.env.MONGODB_URI = 'mongodb://localhost:27017/NodeTodoApp';
} else if (env === 'test') {
  process.env.PORT = 3030;
  process.env.MONGODB_URI = 'mongodb://localhost:27017/NodeTodoAppTester';
}
```

We can now update the `mongoose-config.js` file to connect to the required DB for the current environment, e.g.

```javascript
//connect to MongoDB using Mongoose - use mLab or local uri
mongoose.connect(process.env.MONGODB_URI); // process environment returns mLab uri - url set by process.env in server.js
```

So, when we run our local app the environment will be set to `development`, which will set the MongoDB to `NodeTodoApp`. If then run the tests with Mocha, the environment will be set to `test`, and the test DB will be used.

With this environment structure, we can now maintain a working DB for each environment.

##### app - basic refactor of code structure
A simple refactor of app code.

We can move the environment variables and settings from the current `server.js` file to a separate file, such as `config.js` in a `config` directory.

Then, we simply require this local file in the `server.js` file.

##### app - CRUD API for todos app
So, we can create a todo item, read all or a single todo item, update a single todo item, and finally delete a specified todo item as necessary.

A standard CRUD API for the node-todos-api app.
