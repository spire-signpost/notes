### Node.js - Guide
#### section 7 - API - TODOS & Users app - Testing

A brief outline of testing for a basic API with Node.js &c.

#### Contents
* intro
* authentication - the concept of hashing
* authentication - hashing and salting
* authentication - validate hashed and salted token
* authentication - adding authentication with JSON Web Token (JWT)
* hashing - passwords
* hashing - testing bcrypt

#### intro
For an app, we need to set these routes to private, forcing a user to authenticate successfully prior to using the API.

We can use tokens to help with this authentication of API requests. Upon successful login to the app a user is provided with a token. They can use this token for subsequent authentication for each required API route. e.g. if they need to update or delete a TODO item they created.

Each token will simply be passed as part of the header request for a given route in the API.

#### authentication - the concept of hashing
Hashing is a one-way crypto algorithm. This means that if we provide a string &c. as the input, it will always produce the same hashed result. However, it cannot be reversed.

So, hashing is predictable but cannot be reversed.

For our app, we can install the NPM module `crypto-js`, which includes many cryptography algorithms, e.g.

```javascript
npm install crypto-js --save
```

Then, we can create a new test for hashing,

```javascript
// require npm module - crypto-js for SHA256 hashing algorithm
const {SHA256} = require('crypto-js');

// define input string
var phraseInput = 'the heart of a star';
// hash string using crypto-js
var hashOutput = SHA256(phraseInput);

// console output phrase input
console.log(`Phrase input = ${phraseInput}`);
// console output hash object as string
console.log(`Hash output = ${hashOutput.toString()}`);
```

This will output the following example result,

```bash
Phrase input = the heart of a star
Hash output = 5e01a837cf5a01499644eefca1a9cf97cd7600f451ad7e38ee129b47e3ace435
```

So, in a real world app we can use hashing to check a password entered by a user. A user will simply enter the string for their password, the app will hash that string, and then compare the created hash to the hash stored in the app's DB. This way, we can store the hash in the DB and not the plain text string.

##### authentication - hashing and salting
We can use hashing in our app as follows, e.g.

  * we create an object that contains a property for the user's ID, e.g.

```javascript
var data = {
  id: 7
}
```

This id will tell the app which user can make the request, such as deleting a TODO item for user id 7. So, a user with ID = 4 should obviously not be able to delete or modify a document belonging to a user with ID = 7.

  * we need to ensure that the token we send for the requested user ID = 7 is not modified by the client to another user's ID. If this happened, of course, then the client could simply delete any user's data.
  * to be able to send the user ID to the user, we can create a *token* object that includes the specified data object above plus a hash property. This will simply be the hashed value of the data object. e.g.

```
var token = {
  data,
  hash: SHA256(JSON.stringify(data)).toString()
}
```

This gives us a hashed token, which we can send to the user on the client-side to allow them to authenticate and modify the app for the requested user ID = 7.

However, this token is currently not foolproof.

  * if the user changes the value of the data property in the token to user ID = 3, and then rehash that data, they'll be able to authenticate now as user ID = 3 instead of ID = 7. Again, this would be a serious security flaw in the app and the API.

  * to prevent this modification, we can also **salt** the hash by adding a unique value to the hash that is returned in the token. This unique salting value will only be known by the app, and can then be used to check the value of the hash property in the token. This unique salt value is not known to the user.
  * if the user tries to hash a user ID &c. without this salt value, the app will simply deny authentication to the app and API.

So, we may now create a token as follows,

```javascript
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

So, the user may try to modify the value of the data property to a different user ID, but they can't replicate the expected hash without the value of the salting on the server-side of the app.

##### authentication - validate hashed and salted token
We can run a cursory check for the token, and the expected hashed and salted result. i.e. we can compare the passed token from the client with the expected result on the server. e.g.

```javascript
// create expected token hash - hashed and salted
var serverHash = SHA256(JSON.stringify(token.data) + 'salted').toString();
// compare client and server token hash values
if (serverHash === token.hash) {
  console.log('token hash validated...');
} else {
  console.log('token hash did not validate...do not trust user');
}
```

We can now send a user a token, which has been hashed and salted. When the user tries to authenticate with this token, we can check it on the server side and ensure that it can't be easily changed to a different user's ID.

**n.b.** This standard is known as the JSON Web Token. There are many mature libraries in NPM to help us add this functionality to a live, production app.

##### authentication - adding authentication with JSON Web Token (JWT)
A common option for adding authentication is to use a *JSON Web Token* or JWT. Further info is available at the following URL,

  * https://jwt.io

We can now install a NPM module for JWT, e.g.

```bash
npm install jsonwebtoken --save
```

The benefit of this library is its simplicity of usage for authentication, and its use as an industry standard for authentication.

It allows us to sign a data object, hashed and salted, and then authenticate a request as well. We can use two methods for this purpose, `sign()` and `verify()`.

`sign()` can be called on the data object that contains the user request, e.g. their user ID. This will create a signed token that is hashed and salted. e.g.

```javascript
const jwt = require('jsonwebtoken');

var data = {
  id: 7
};
// pass data object and secret for salting...
var token = jwt.sign(data, 'salted');
// console output token
console.log(token);
```

To verify this token for subsequent requests, we can then use the JWT method, `verify()`. e.g.

```javascript
// pass token to verify with secret for salting...
var tokenDecode = jwt.verify(token, 'salted');
// console output decoded token
console.log('token decoded = ', tokenDecode);
```

An example return is as follows,

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NywiaWF0IjoxNTAyNDc2MDA0fQ.hu_B9qzOtW3Mlu1YYbcWDf-3S3PxcydbdwwArjCTwJg
token decoded =  { id: 7, iat: 1502476004 }
```

The return object for the decoded token includes the original ID from the data object, i.e. id = 7, and the `iat` or *issued at timestamp*. This timestamp basically records when the token was created.

This return is known as the *payload* in JWT.

##### hashing - passwords
A user will send a plain text version of their password when submitting a request to the POST `/users/me` request. This plain text password will be validated. If validation is successful, the app will then hash the password, salt it as well, before saving it to the database.

The hashing will be completed using an algorithm called *bcrypt*, which has salting built in as well.

We can install a NPM module for *bcrypt*, e.g.

```bash
npm i bcryptjs --save
```

There are many *bcrypt* modules available, including the popular *bcryptjs*. Further details on *bcryptjs* is available at the following URL,

  * https://www.npmjs.com/package/bcryptjs

##### hashing - testing bcrypt
We'll start with a few basic tests for using the *bcryptjs* module in a new test file in the playground directory, e.g. `playground/hash-tests/hash-bcrypt.js`.

We'll start by requiring this module as usual,

```javascript
const bcrypt = require('bcryptjs');
```

and then we can add an initial test, e.g.

```javascript
// test pasword string
var password = "abcd1234";
```

So, we can now test salting and hashing a password. We start by generating a salt value using *bcryptjs*.

`bcrypt.genSalt()` takes two arguments, which include a callback function for the second. The first argument is the number of rounds to use to generate the salt. The more rounds, the slower the bcrypt algorithm performs. However, slower may be preferable for passwords, as it will also prevent continuous brute force attacks on the server.

So, this salting is as follows, e.g.

```javascript
// generate salt and then hash - 15 rounds to salt password
bcrypt.genSalt(15, (error, salt) => {
  // three arguments for hash() - password to hash, salt just created, and callback for success and failure
  bcrypt.hash(password, salt, (error, hash) => {
    console.log(hash);
  });
});
```

With this example, we create a salt usig 15 rounds, and then hash the plain text password with the generated salt value. We can then console output the hash value (with salt) to check expected return.

If we run this test example, we'll get a hash value with salt output to the console, e.g.

```bash
$2a$15$1k.7jAGIqXSbMknskqzvCeCFjv.bENpB1kd/R7.6PJUlby/fQIpOu
```

This hash value can then be saved to a DB &c. as a way to authenticate a user.

We can test this hash against a passed plain text password, i.e. from a user submission, as follows,

```javascript
// test hashed password
var hashedPassword = '$2a$15$1k.7jAGIqXSbMknskqzvCeCFjv.bENpB1kd/R7.6PJUlby/fQIpOu';
// three arguments - plain text password, hashed password, and callback function for success or fail
bcrypt.compare(password, hashedPassword, (err, res) => {
  // console output boolean result of comparison
  console.log(res);
});
```

The compare function expects the hashed password, the plain text password sent by the user, and then returns whether they match.

The third argument is a callback function, which will either return an error with the function itself or a boolean value for the comparison. If the passwords match then the boolean will be set to TRUE, and FALSE if they do not.

So, we'll use *bcrypt* as follows,

  * user submits initial plain text password
  * bcrypt will hash and salt plain text password
  * hashed password saved to DB
  * user submits plain text password to login to app
  * hashed password is retrieved from DB for current user
  * bcrypt compares passwords and returns boolean for match or no match
  * user is either allowed to login or refused entry...
