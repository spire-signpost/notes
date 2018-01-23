### Node.js - Notes API
#### getting started & setup

A brief outline of setup and config for a NOTES API with Node.js &c.

#### Contents
* intro
* local project setup
* Mongoose - setup
* v0.1
  * initial app setup
  * Mongoose - create a model
  * Mongoose - use a model
* v0.2
  * refactor code for Mongoose
  * refactor code for models &c.
  * add API for notes - setup server
  * add API for notes - POST route
* v0.3
  * add API for notes - GET route
  * add API for notes - GET route with parameter
  * add validation &c.
  * Mongoose - call `findById()`
* v0.4
  * Heroku & MongoDB
  * deploy app with DB support
* v0.5
  * DELETE route and options
  * add API for notes - DELETE route with parameter
* v0.6
  * add utility module - Lodash
  * add API for notes - PATCH route with parameter
* v0.7
  * modify note for `updatedAt` and `createdAt`
* v0.8
  * add user model and access
  * add API for users - POST route for adding users
* Authentication - Hashing, Salting &c.
  * hashing
  * hashing and salting
  * adding authentication with JSON Web Token (JWT)
* v0.9
  * add API for users - add auth tokens
  * Schema for user model
  * Schema usage for user & update POST route for users
* v0.9.1
  * add API for users - private route for GET `/users/me`
* v0.9.2
  * authentication - abstract and refactor private route
  * authentication - bcrypt hashing and salting
* v0.9.3
  * add API for users - POST /users/login
* v0.9.4
  * add API for users - DELETE /users/me/token
* v0.9.5
  * update NOTES with private routes
  * update routes for POST, GET. DELETE, PATCH
* v0.9.6
  * update app config
  *

#### intro
Combine Node.js, Mongoose, and MongoDB to create a NOTES API.

#### local project setup
Create a local directory for the project, e.g.

```bash
mkdir /development/nodejs/node-notes-api
```

Then, initialise a Node.js based app

```bash
npm init
```

This will create an initial `package.json` file with metadata for the current project.

We also need to initialise a local Git repository for the project, which helps with version control and Heroku setup and hosting

```bash
git init
```

#### Mongoose - setup
In the local project directory, install Mongoose

```bash
npm i mongoose --save
```

#### v0.1 - initial app setup
Add a `server` folder to the root of the project, and then add a `server.js` file for the Express based server config.

We can start by requiring Mongoose, connecting to a DB, and specifying a Promise library.

e.g.

```js
// require mongoose
var mongoose = require('mongoose');

// specified preferred promise library
mongoose.Promise = global.Promise;

//connect to MongoDB using Mongoose
mongoose.connect('mongodb://localhost:27017/NodeNotesApp');
```

#### v0.1 - Mongoose - create a model
We need to create a model for notes data stored in MongoDB. Initially, add this to `server.js`

e.g.

```js
// specify model for note item
var Note = mongoose.model('Note', {
  text: {
    type: String,
    required: true, // text property required to create a new note item
    minLength: 1, // note item must have at least one character
    trim: true // removes any leading or trailing spaces
  },
  createdAt: {
    type: Number,
    default: null
  }
});
```

This example model includes the use of built-in validators provided by Mongoose. e.g. we can ensure some text is added for a note, set a minimum length for the note, and remove unnecessary white space as well. We can also use lots of other validators, such as a `default` value.

#### v0.1 - Mongoose - use a model
Use this new model to create note items in the DB.

Model is used as a constructor to create a new Note object, which we may add initially to the `server.js` file. e.g.

```js
// create a new Todo item from the model
var noteItem = new Note({
  text: 'a test note for Node-notes-api app...',
  createdAt: 281217
});
```

and then use it to save the note to the DB, e.g.

```js
// save the object as a document in the DB - save returns a promise
noteItem.save().then((doc) => {
  console.log('note item saved', doc);
}, (error) => {
  console.log('note item not saved: ', error);
});
```

We can then test this initial setup using the following command,

```bash
node ./server/server.js
```

#### v0.2 - refactor code for Mongoose
Add a new directory for DB config files, `/server/dbms` including a config file for Mongoose `mongoose-config.js`.

This file should include config and connection settings &c. for Mongoose to MongoDB.

So, in `server.js` we remove the previous Mongoose settings and config, and add the following to import settings

```js
// local - get mongoose property using ES6 destructuring - name of created local variable will match the property of the object
var {mongoose} = require('./dbms/mongoose-config.js'); // require custom mongoose config created for app
```

We can add the following to the new `mongoose-config.js` file

```js
// require Mongoose
var mongoose = require('mongoose');

// specified preferred promise library
mongoose.Promise = global.Promise;
//connect to MongoDB using Mongoose
mongoose.connect('mongodb://localhost:27017/NodeNoteApp');

// module exports
module.exports = {
  mongoose // ES6 shortcut for mongoose: mongoose
};
```

#### v0.2 - refactor code for models &c.
We also need to refactor code for models &c. in `server.js`.

Create a new directory `/server/models`, and add a new model for notes, `note-model.js`. We can then move the following from `server.js` to this new file

```js
// specify model for note item
var Note = mongoose.model('Note', {
  text: {
    type: String,
    required: true, // text property required to create a new note item
    minLength: 1, // note item must have at least one character
    trim: true // removes any leading or trailing spaces
  },
  createdAt: {
    type: Number,
    default: null
  }
});
```

We then export this model, and require it in the `server.js` file as follows,

```js
// note for model
var {Note} = require('./models/note-model.js');
```

#### v0.2 - add API for notes - setup server
Now create and add a basic API for working with the *notes*. e.g. routes for GET, POST &c.

Add the Express module to our current app,

```bash
npm i express --save
```

and the module `body-parser`

```bash
npm i body-parser --save
```

Then, require modules in `server.js`, e.g.

```js
// express web framework
var express = require('express');
// body-parser module
var bodyParser = require('body-parser');
```

And, we can add basic logic for an Express-based server, e.g.

```js
...
// create express app
var app = express();

// set port for server
app.listen(3030, () => {
  console.log('server started on port 3030...');
});
```

#### v0.2 - add API for notes - POST route
Add a POST route to save a *note*. e.g.

```js
app.post('/notes', (req, res) => {
  ...
});
```

First, we need to configure the `bodyParser` middleware, which parses the body (i.e. of a note) into a JS object. This is then attached to the `req` object for the route.

So, we can now create a new Note item using the Node model, e.g.

```js
app.post('/notes', (req, res) => {
  // create note item from model
  var note = new Note({
    text: req.body.text // specify text for each note item
  });

  note.save().then((doc) => {
    res.send(doc); // send back saved document details
  }, (error) => {
    // send back errors...
    res.status(400).send(error); // send back error and status code for request...
  })
});
```

#### v0.3 - add API for notes - GET route
Add a GET route to find and return all note items, e.g.

```js
// GET route for note items
app.get('/notes', (req, res) => {
  Note.find().then((notes) => { // promised resolved with all of the notes from the db
    res.send({ // response - send data back from get route - all of the notes
      notes // add notes array to object - update and modify object as needed instead of just sending array response...
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

and test the GET route at `http://localhost:3030/notes`

#### v0.3 - add API for notes - GET route with parameter
Add an extra GET route with an additional parameter, e.g. for a specific individual note

  * http://localhost:3030/notes/4321

This parameter is available as a property of the `req` object, e.g.

```js
// GET route with parameter
app.get('/notes/:id', (req, res) => {
  // get params from req
  var params = req.params;
  console.log(params);
});
```

This will return an object with a name:value pair, e.g.

```json
{
  "id": "4321"
}
```

#### v0.3 - add validation &c.
This route uses data from user input, the Note item ID, so we need to add some validation &c.

Add the `ObjectID` property from MongoDB object (its native driver),

```js
// ObjectID for validation
var {ObjectID} = require('mongodb');
```

We can then use this to check if passed parameter is valid,

```js
// validate passed ID - check not valid
if (!ObjectID.isValid(params_id)) {
  // return 404 status code for invalid ID
  return res.status(404).send();
}
```

#### v0.3 - Mongoose - call `findById()`
Then, we can use Mongoose's `findById()` to the data by ID parameter, e.g.

```js
Note.findById(params_id).then((note) => {
  // check if return data available
  if (!note) {
    return res.status(404).send();
  }

  // otherwise return the data for the params ID
  res.send({note}); // return todo in object - more flexible than default array return
}).catch((error) => { // catch return errors for query
  res.status(400).send();
})
```

The GET route with parameter ID will now either return a success object or catch an error.

#### v0.4 - Heroku & MongoDB
To publish this app with Heroku and live DB support, we need to add the following initial modifications

##### `server.js`
  * add environment port variable - Heroku can dynamically set port number of app or app will default to local 3030
  * `app.listen()` - updated to use variable `port` for dynamic port setting

e.g.

```js
// environment port variable
const port = process.env.PORT || 3030;
```

and

```js
// set port for server
app.listen(port, () => {
  console.log(`server started on port ${port}`);
});
```

##### `package.json`
  * add `start` to scripts - tells Heroku how to run our app
    * `"start": 'node server/server.js'`
  * specify node version to use for hosted app
    * add `engines` with current version used for developing app on local system
    * e.g. `"engines": {"node": "8.2.1"}`

e.g.

```json
"scripts": {
  "start": "node server/server.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
"engines": {
  "node": "9.3.0"
},
```

#### v0.4 - Heroku & MongoDB - deploy app with DB support
Create a remote instance of MongoDB using *mLab MongoDB*. We can install a Heroku add-on to help connect these hosted services.

Further details on Heroku add-ons,

  * https://elements.heroku.com/addons

To install this add-on we can run the following command in our project's CWD,

```
heroku create //create a new heroku app if necessary
heroku addons:create mongolab:sandbox //install add-on for mLab MongoDB with free sandbox plan
```

mLab offers a free sandbox option, which is installed for our app.

##### modify Mongoose config
Modify `server/dbms/mongoose-config.js` to add a dynamic URL for the DB. This is updated to include either the return URL for mLab or the current default for the local machine, e.g.

```js
//connect to MongoDB using Mongoose - use mLab or local uri
mongoose.connect(process.env.MONGODB_URI || 'mongodb://localhost:27017/NodeNoteApp'); // process environment returns mLab uri
```

Then, add the updates to the local Git repository, commit the changes, and push to Heroku.

#### v0.5 - DELETE route and options
Add a route to remove/delete a todo item document by specified ID parameter.

Mongoose provides various methods for removing/deleting documents from MongoDB, e.g.

* remove all docs from the DB, e.g.

```js
// remove all docs from DB
Note.remove({}).then((result) => { // empty object required to delete all docs
  consol.log(result);
});
```

* find a doc by ID and then delete it, e.g.

```js
// find a single doc by ID and remove...
Note.findByIdAndRemove('597b92e6031086379d868696').then((note) => {
  console.log(note);
});
```

* find a document by another property (instead of ID), e.g.

```js
// find a single doc and remove from db - single doc removed will also be returned
Note.findOneAndRemove({createdAt: 311217}).then((note) => { // useful to find doc without ID - i.e. by text, author &c.
  console.log(note);
});
```

*n.b.* still only delete a single doc, the first found in the DB.

#### v0.5 - add API for notes - DELETE route with parameter
Update `server.js` to include a DELETE route with ID parameter for note item

```js
app.delete('/notes/:id', (req, res) => {
  // get params ID from req
  var params_id = req.params.id;
  console.log(params_id);

  // validate passed ID - check not valid
  if (!ObjectID.isValid(params_id)) {
    // return 404 status code for invalid ID
    return res.status(404).send();
  }

  // find doc by ID and remove from DB
  Note.findByIdAndRemove(params_id).then((note) => {
    // check if return data available
    if (!note) {
      return res.status(404).send();
    }
    // otherwise return the data for the deleted params ID
    res.send({note}); // return todo doc deleted...
  }).catch((error) => { // catch return errors for the query
    res.status(400).send();
  });
});
```

This route follows same pattern as GET with parameter. We simply use a different method with the requested ID to remove the doc from the DB.

#### v0.6 - add utility module - Lodash
Add the JS utility library, *Lodash*

```bash
npm i lodash --save
```

Then, we need to require this library in the `server.js` file,

```js
const _ = require('lodash');
```

#### v0.6 - add API for notes - PATCH route with parameter
Add a PATCH route for items with an ID parameter, e.g.

```js
// PATCH route for single doc with ID
app.patch('/notes/:id', (req, res) => {

});
```

Then, add some required variables

```js
// get params ID from req
var params_id = req.params.id;
console.log(params_id);
// only pick the properties we need for an update - stops false, unnecessary &c. properties being sent by the user
var body = _.pick(req.body, ['text']); // pick method from lodash - gets only specified properties from return req
```

In this example, we get the required doc ID from the route params. Then, we create a `body` variable for the data to update. We may restrict properties using Lodash's `pick()` method with the `req` object.

Then, check if requested ID is valid or not (as with other routes), e.g.

```js
// validate passed ID - check not valid
if (!ObjectID.isValid(params_id)) {
  // return 404 status code for invalid ID
  return res.status(404).send();
}
```

Then, we need to update the doc in the DB, e.g.

```js
// update the requested doc in the db - using Mongoose method, findByIdAndUpdate()
Note.findByIdAndUpdate(params_id, {$set: body}, {new: true}).then((note) => { // MongoDB update - set object to body, and return the new doc object - new: true (mongoose naming for returnOriginal)
  // check doc object exists - return 404 for not found
  if (!note) {
    return res.status(404).send();
  }
  // if doc found - send doc object
  res.send({note});
}).catch((error) => { // catch error
  res.status(400).send(); // send back error status - bad request code
});
```

#### v0.7 - modify note for `updatedAt` and `createdAt`
We can update the model for a `note` to save the current date and time for creation of a note, and each time a note is updated.

In the `note-model.js`, modify `note` as follows

```js
...
var currentDate = new Date().getTime();
// specify model for note item
var Note = mongoose.model('Note', {
  text: {
    type: String,
    required: true, // text property required to create a new note item
    minLength: 1, // note item must have at least one character
    trim: true // removes any leading or trailing spaces
  },
  createdAt: {
    type: Number,
    default: currentDate
  },
  updatedAt: {
    type: Number,
    default: null
  }
});
```

So, we get the current date and time, and then set it as the default value for `createdAt`. We can also add a new `updatedAt` property.

As the note is created with the POST route in the API, the `createdAt` value will be set to the current unix timestamp in milliseconds.

When we update a notr with the PATCH route, the `updatedAt` value will be set to the current date and time as well. The PATCH route will be updated as follows,

```js
body.updatedAt = new Date().getTime(); // returns no. of ms from midnight 1 jan 1970 to current date...
```

#### v0.8 - add user model and access
To add users to the app, we need a Mongoose model for each user. So, each user will send the following object

e.g.

```js
{
  email: 'test@test.com', // validated to check email structure
  password: 'jkhujshunkji897hhuh7', // password from user -
  tokens: [{
    access: 'auth',
    token: 'kjdisknksanudnjnasnlasnck'
  }]
}
```

This model includes:

  * email - validated to check correct structure to match defined emails
  * password - sent from the user (client-side) as plain text
    * hashed on the server-side using the *BCrypt* algorithm - a one-way hash that can't be reversed from hash
  * tokens - array of objects for login
    * each token will have access type - e.g. auth
    * each token will have the token value itself - a token string that is passed back and forth to check credentials for access &c.

Each email needs to be *validated* - we can add a validator tool to help with this task,

```bash
npm i validator --save
```

We can then check email validity in the model,

```js
// specify model for user
var User = mongoose.model('User', {
  email: {
    type: String,
    required: true,
    trim: true,
    minlength: 1,
    unique: true, // checks that email is unique in current system
    validate: {
      validator: (value) => {
        return validator.isEmail(value);
      },
      message: '{VALUE} is not a valid email'
    }
  }
});
```

Then, we can add `password` to the model,

```js
...
password: {
  type: String,
  require: true,
  minlength: 6 // min length for user password
}
...
```

We can also add an array for *tokens* - adds an object that defines the properties we need for a given token.

e.g. `access` and the actual `token`

```js
...
tokens: [{
  access: {
    type: String,
    required: true
  },
  token: {
    type: String,
    required: true
  }
}]
...
```

#### v0.8 - add API for users - POST route for adding users
Add a POST route to `server.js` for adding new users - route will use `/users`.

```js
// POST route for adding a user - /users
app.post('/users', (req, res) => {
  // use Lodash pick method - retrieve specific properties of the API request
  var body = _.pick(req.body, ['email', 'password']);
  // new instance of user model - pass picked values from request
  var user = new User(body);
  // save user to DB - then handle response - & catch any errors
  user.save().then((user) => {
    res.send(user);
  }).catch((error) => {
    res.status(400).send(400);
  })
});
```

We can then add authentication to this route as continue to build the user routes for the API.

We can also test this route using Postman, sending an example user registration, e.g.

```js
{
  "email": "test@test.com",
  "password": "1234defg"
}
```

So, a sample return might be as follows

```js
{
  "__v": 0,
  "email": "test@test.com",
  "password": "1234defg",
  "_id": "5a4d4241dab9f50014018773",
  "tokens": []
}
```

#### Authentication - Hashing, Salting &c.
Use tokens to help with the authentication of API requests.

A user logs in successfully, and they will receive a token for accessing required API routes. Each token is passed in a route's header request.

##### hashing
Hashing is a one-way crypto-algorithm. e.g. provide a string as input, and return a hashed result. A benefit is that it cannot be easily reversed.

We can use a NPM module, `cypto-js`, which provides many cryptography algorithms, e.g.

```js
npm i crypto-js --save
```

Example usage scenario is as follows,

* a user enters a string password
* the app will *hash* that string
* the app will compare this *hash* to the stored *hash* in the app's DB

The benefit is that we store the hashed password, and not the plain text.

##### hashing and salting
We may create a hashed token, which we send to the user on the client-side. This allows the user to authenticate and modify the app relative to their permissions in the app.

However, hashing is not a *fool-proof* solution. A hashed token may be modified, so we may need to also *salt* the hash. This adds a unique value to the hash returned in the token.

The unique salt value is only known by the app, and is used to verify the returned token. The unique salt value is not known to the user.

For example, we may create a token for passed data with hashing and salting, e.g.

```js
// user id
var data = {
  id: 7
};
// generated token for the id data - hash and salting
var token = {
  data,
  hash: SHA256(JSON.stringify(data) + 'salted').toString()
}
```

We use the user-id in the data to check user modifications to a note &c. However, the authentication has now been hashed and salted. The user may try to modify the user-id to a different ID, but the user can't replicate the expected hash without the salt value.

**n.b.** this standard is known as the JSON Web Token. There are many mature libraries in NPM to help us add this functionality to a live, production app.

##### adding authentication with JSON Web Token (JWT)
A common option for adding authentication is to use a *JSON Web Token* or JWT. Further info is available at the following URL,

  * https://jwt.io

We can now install a NPM module for JWT, e.g.

```bash
npm install jsonwebtoken --save
```

This library allows us to sign a data object, hashed and salted, and then authenticate a request as well. We can use two methods for this purpose, `sign()` and `verify()`.

`sign()` creates a signed token that is hashed and salted. e.g.

```js
const jwt = require('jsonwebtoken');

var data = {
  id: 7
};
// pass data object and secret for salting...
var token = jwt.sign(data, 'salted');
// console output token
console.log(token);
```

`verify()` will check this token for subsequent requests, e.g.

```js
// pass token to verify with secret for salting...
var tokenDecode = jwt.verify(token, 'salted');
// console output decoded token
console.log('token decoded = ', tokenDecode);
```

An example return from these methods is as follows,

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NywiaWF0IjoxNTAyNDc2MDA0fQ.hu_B9qzOtW3Mlu1YYbcWDf-3S3PxcydbdwwArjCTwJg
token decoded =  { id: 7, iat: 1502476004 }
```

Return object for the decoded token includes the original ID from the data object, i.e. id = 7, and the `iat` or *issued at timestamp*. This timestamp basically records when the token was created.

This return is known as the *payload* in JWT.

#### v0.9 - add API for users - add auth tokens
We need a return token for each new user, which we can add to the POST route `/users` in `server.js`.

#### v0.9 - Schema for user model
By creating a reusable schema for the user model, we can add customised methods to the model. This helps to abstract usage, and restricts method access to objects of the model.

So, we can update `user-model.js` to create a schema, which we can then use to validate and create a new `User` model.

##### schema for user
The user schema is as follows,

```js
var UserSchema = new mongoose.Schema({
    email: {
      type: String,
      required: true,
      trim: true,
      minlength: 1,
      unique: true, // checks that email is unique in current system
      validate: {
        validator: (value) => {
          return validator.isEmail(value);
        },
        message: '{VALUE} is not a valid email'
      }
    },
    password: {
      type: String,
      require: true,
      minlength: 6 // min length for user password
    },
    tokens: [{
      access: {
        type: String,
        required: true
      },
      token: {
        type: String,
        required: true
      }
    }]
});
```

##### add method to schema
We can then add a method to the schema, which only accessible to the model and an instantiated object.

```js
// add instance method to UserSchema objects - needs standard function syntax to provide `this` keyword
UserSchema.methods.generateAuthToken = function () {
  var user = this; // provides access to a document - the document this method was called against...
  // get access value from tokens in schema
  var access = 'auth';
  // create token for user from schema
  var token = jwt.sign({_id: user._id.toHexString(), access}, 'salted').toString(); // temporary secret value
  // update tokens array in schema - user will now be generated with the created token values...
  user.tokens.push({access, token});
  // save user - returns promise in server.js where it will be called and used...
  return user.save().then(() => {
    return token; // returns value as success value for next then() in server.js call
  });
};
```

and instantiate a `User` object, e.g.

```js
var User = mongoose.model('User', UserSchema);
```

#### v0.9 - Schema usage for user & update POST route for users
Use the updated model and method in `server.js` with the POST route for adding users, `POST /users`.

When we save a user, the route is updated as follows, e.g.

```js
// POST route for adding a user - /users
app.post('/users', (req, res) => {
  // use Lodash pick method - retrieve specific properties of the API request
  var body = _.pick(req.body, ['email', 'password']);
  // new instance of user model - pass picked values from request
  var user = new User(body);
  // save user to DB - then handle response - & catch any errors
  user.save().then(() => {
    // call method from user model - use return token in promise chain...
    return user.generateAuthToken();
  }).then((token) => {
    // send back user with the updated token...and send custom header for auth token
    res.header('x-auth', token).send(user); // `x-...` in a header indicates a custom header...
  }).catch((error) => {
    res.status(400).send(error);
  })
});
```

The generated token may be used to set the header for the response when we send the user object back. We're setting a custom header, denoted by `x-`, and setting the token as part of this custom header.

#### v0.9 - override method in user model for security
We need to add further security and restrictions to the values returned in the user's visible object. At the moment, we can see the return for a newly created user - including password and tokens array.

So, user object properties need to be restricted to email and ID.

We need to update `user-model.js` to override a JSON method, e.g.

```js
UserSchema.methods.toJSON = function () {
  var user = this;
  // take mongoose object and convert to regular object - only properties from user object will exist
  var userObject = user.toObject();
  // return specific user object properties - use pick method from lodash
  return _.pick(userObject, ['_id', 'email']); // only return id and email for a user object...
};
```

We can use Lodash to `pick` the required properties, ID and email, which restricts the return values for the response object.

The headers remain the same and unaffected, which includes the token for the user.

#### v0.9.1 - add API for users - private route for GET `/users/me`
Add new route for users to show details for authenticated user. Route is private, authenticated with `x-auth` token from header.

This new route is as follows,

```js
app.get('/users/me', (req, res) => {
  // get token from header - req will sent for the returned header
  var token = req.header('x-auth'); // key from the header is `x-auth`

  // call user model method with token - check return promise for user
  User.findByToken(token).then((user) => {
    // check user return
    if (!user) {
      // reject promise - code execution stops and exits...
      return Promise.reject();
    }

    // success - user authenticated token...
    res.send(user);
  }).catch((error) => {
    // send back 401 status - error code
    res.status(401).send();
  });
});
```

So, we start by getting the header with the token for authentication. Then, we call an abstracted function, `findByToken()`, which we may create for the User model in `user-model.js`.  

This new function needs to verify the token, authenticate the user, and then return some data &c. e.g.

```js
// model method for token authentication - statics defines method as a models method
UserSchema.statics.findByToken = function (token) {
  // user model = `this` binding - because this is a model method...
  var User = this;
  // store decoded jwt values
  var decoded;

  // catch any errors for verify()
  try {
    decoded = jwt.verify(token, 'salted'); // pass token to verify plus secret phrase for salting...

  } catch (error) {
    return new Promise((resolve, reject) => {
      reject();
    });
  }

  // return promise to query (i.e. in server.js) for requested user values
  return User.findOne({
    _id: decoded._id,
    'tokens.token': token, //quotation marks required due to period in tokens.token
    'tokens.access': 'auth'
  });
};
```

We're adding a method to the User model for authentication by token. `statics` is used to ensure this method is set on the model.

`this` is set relative to the User model. If the authentication and query are successful, the method returns a *promise* object for the user. This return will include the user's ID, the token, and auth access for the app's routes.

So, to use this method we need to send a valid `x-auth` header.

For error handling in this method, we can return a *promise* with a simple `reject` value.

This can then be used in `server.js` to catch errors in the deferred chain, which will simply return a status code of `401` to confirm an error.

Likewise, in the `/users/me` route we can return a *promise* with a reject value if the user is not found.

#### v0.9.2 - authentication - abstract and refactor private route
Add custom middleware to abstract authentication for multiple routes.

Create a new `authenticate()` function, and move a lot of the auth code from `app.get()` route, e.g.

```js
// three arguments for middleware - request, response, next
var authenticate = (req, res, next) => {
  // get token from header - req will sent for the returned header
  var token = req.header('x-auth'); // key from the header is `x-auth`

  // call user model method with token - check return promise for user
  User.findByToken(token).then((user) => {
    // check user return
    if (!user) {
      // reject promise - code execution stops and exits...
      return Promise.reject();
    }

    // modify request object - user
    req.user = user; // set user in request object to user just found...
    // modify request object - token
    req.token = token; // set token to above token from `x-auth` header
    // call next to run code in function that calls this middleware
    next();
  }).catch((error) => {
    // send back 401 status - error code
    res.status(401).send();
  });
};
```

So, the main change with authentication handling in this abstracted function is the user and token. The found `user` and `token` are added to the `req` object. This can then be used in an API route that requires authentication.

In the project, add a new folder for `middleware`, and create a new file `authenticate.js`. Then, require this file in `server.js`

```js
// require authenticate from local module
var {authenticate} = require('./middleware/authenticate');
```

We can then use this new middleware in `server.js` as follows,

```js
app.get('/users/me', authenticate, (req, res) => {
  // send the user from the authenticated request
  res.send(req.user);
});
```

#### v0.9.2 - authentication - bcrypt hashing and salting
Add *bcrypt*, and update the `user` model for the app.

```bash
npm i bcryptjs --save
```

To ensure hashing is completed before the app save a document, we can add management for the middleware. We can use Mongoose middleware, which allows us to execute code before and after the user model.

e.g. before data is saved to the DB,

```js
// run some code before the schema executes save...e.g. hash passwords before save
UserSchema.pre('save', function(next) {
  // access document
  var user = this;
  // check if password is modified - if yes, then hash and salt is required
  if (user.isModified('password')) {
    // hash and salt password - before running save
    bcrypt.genSalt(15, (error, salt) => { // generate salt
      // hash password with salt
      bcrypt.hash(user.password, salt, (error, hash) => {
        // update user document with the hashed and salted password
        user.password = hash;
        // call next() to continue execution of schema - doc will be saved with hashed password...
        next();
      });
    });
  } else {
    // if not modified - continue schema execution
    next();
  }
});
```

So, we call the method `pre()` on the UserSchema, and then use bcrypt. To continue with the execution of the schema, we can simply call the function `next()`.

Require bcrypt in `user-model.js`,

```js
// require npm module - bcrypt hashing and salting
const bcrypt = require('bcryptjs');
```

#### v0.9.3 - add API for users - POST /users/login
Add a new POST route for user login.

We can allow a user to login with the email and password they used for registration. The DB will be queried for the email and a hashed version of the password.

As part of the login, the user provided plain text password will be hashed and salted. Then, we can use a *bcrypt* compare method to compare this password with the saved copy in the DB.

Initial route is as follows, e.g.

```js
// POST route for user login - /users/login
app.post('/users/login', (req, res) => {
  // pick data for email and password from request body
  var body = _.pick(req.body, ['email', 'password']);

  // check data is being picked correctly
  res.send(body);
});
```

We're checking that the data is requested correctly. Then, we need to verify that a user exists for the passed email, and check the hashed version of the password in the DB.

So, for the email verification we can add a new model method for a user, e.g.

```js
// static for a model method - standard function call to use `this`
UserSchema.statics.findByCredentials = function (email, password) {
  var User = this;
  // query db for user with passed email - then verify password...
  return User.findOne({email}).then((user) => {
    // check if user exists
    if (!user) {
      // reject the promise if no user exists
      return Promise.reject();
    }

    // return Promise for user found in db
    return new Promise((resolve, reject) => {
      // resolve Promise for user found...3 args expected
      bcrypt.compare(password, user.password, (err, res) => {
        if (res) {
          // if response is sent for user
          resolve(user); // resolve the promise
        } else {
          // handle the error from the request
          reject() // reject the promise - sends a 400 status code
        }
      });
    });
  });
};
```

This model method is then called in the API route in `server.js`, e.g.

```js
// POST route for user login - /users/login
app.post('/users/login', (req, res) => {
  // pick data for email and password from request body
  var body = _.pick(req.body, ['email', 'password']);

  // find the user by email and password - use return user object
  User.findByCredentials(body.email, body.password).then((user) => {
    // get a new token for the user - use abstracted function created for user save...
    return user.generateAuthToken().then((token) => {
      // set the header to x-auth with token, and send response body back as `user`
      res.header('x-auth', token).send(user);
    });
  }).catch((error) => {
    // tell user they were unable to login...
    res.status(400).send(); // send status of 400 for error...
  });
});
```

#### v0.9.4 - add API for users - DELETE /users/me/token
Add a route to enable user logout from the app. In effect, the route will *revoke* the token for the current user.

This DELETE route is private to ensure only the authenticated user can themselves logout. Therefore, there's no need to read or load the auth token from the body of a response.

So, if a user is *not* authenticated, they will not be able to view the route.

Route is as follows, e.g.

```js
// DELETE route for user logout - /users/me/token
app.delete('/users/me/token', authenticate, (req, res) => { // use authenticate to make the route private
  // delete token set in the authentication middleware - access user via req.user as the user is already authenticated...
  req.user.removeToken(req.token).then(() => { // call instance method to abstract deletion of token
    // respond with 200 status code - OK
    res.status(200).send();
  }, () => { // second callback to then()
    // respond with 400 status code for error &c.
    res.status(400).send();
  });
});
```

We pass a call to `authenticate`, thereby making the route *private*. Then, we call a custom method from the user model on the `req` object passing the token for the current user, which is available on the `req` object itself.

Then, we handle the success callback for the deleted token.

Custom method on the user model is as follows to remove a token, e.g.

```js
// custom instance method - pass token to delete
UserSchema.methods.removeToken = function (token) {
  // lowercase `this` for instance method
  var user = this;

  // call update() on object to update - pass updates object
  return user.update({
    // specify what to `pull` from db - pull from tokens array
    $pull: {
      tokens: {
        token: token
      }
    }
  });
};
```

To remove the actual token for the authorised user, we can use the MongoDB operator, `$pull`. This operator allows us to remove items from a db that match certain criteria.

In this example, we're checking the `tokens` array for a token property that matches the current token. If it matches the whole `tokens` object will be removed, and not just the single property. So, any other data stored in the `tokens` array will also be removed.

We can then `return` this updated user, so we can chain any call to this instance method elsewhere in the app.

#### v0.9.5 - update NOTES with private routes
To make notes private, we're effectively linking specific note items with a user.

Therefore, we need to update the `note` model as well, to ensure we link a user's ID to a specific note item in the DB.

Then, as a user requests their note/s, private access is granted to the required document/s in the DB.

##### update note model

So, we can add the following to the schema for a note model,

```js
...
author: {
  type: mongoose.Schema.Types.ObjectId,
  required: true
}
...
```

We can use Mongoose's Schema constructor to access the `Types` object with a property of `ObjectId`. This can be used to set the type for this model property.

A user may not, therefore, create a note item unless they have authenticated with the app.

##### update `note` routes to private
In `server.js`, we can update a note route to private as follows, e.g. POST `/notes`

```js
// 1. POST route for note
app.post('/notes', authenticate, (req, res) => {
  // create note item from model
  var note = new Note({
    text: req.body.text, // specify text for each note item
    author: req.user._id // add user id for authenticated user - add privacy to route...
    createdAt: req.body.createdAt
  });

  note.save().then((doc) => {
    res.send(doc); // send back saved document details
  }, (error) => {
    // send back errors...
    res.status(400).send(error); // send back error and status code for request...
  })
});
```

and then update the GET `/notes` route to private, e.g.

```js
// 2. GET route for notes
app.get('/notes', authenticate, (req, res) => {
  Note.find({
    author: req.user._id
  }).then((notes) => { // promised resolved with all of the notes from the db
    res.send({ // response - send data back from get route - all of the notes
      notes // add notes array to object - update and modify object as needed instead of just sending array response...
    });
  }, (error) => { // error callback if error with promise
    res.status(400).send(error); // send back error and status code for request...
  });
});
```

So, for each route, we've added the `authenticate` middleware to the request, and then ensure that we only fetch note items that match the ID of the authenticated user.

##### test private routes in Postman
We can then test these routes in Postman as follows,

  * signup a new user - POST `/users`
    * gives x-auth token to use in other API routes
    * useful to create at least two users - easier to test private docs linked to user id
  * create a new note item - POST /notes
    * use token to add author id for private doc
    * add x-auth header with user token
    * test create without x-auth token - 401 unauthorized code returned...
  * fetch notes for user id specified - GET `/notes`
    * send x-auth header token for required user
    * retrieve note items for logged in user
      * should only return note items for header x-auth token...

##### add privacy to all API routes
We can then secure the API for all such NOTE routes, including

* GET by ID
* DELETE by ID
* PATCH by ID

The general usage and updates follow the same pattern as above for private routes. However, we need to change methods from `findById()` to `findOne()`, otherwise a logged in user would be able to find any note simply by ID. So, we now pass the ID and the author property to ensure only the original author can modify the specified note.

We follow a similar pattern for DELETE and PATCH, again updating method calls from ID to One.

#### v0.9.6 - update app config


#### v0.9.6 - update JWT config to hide settings
