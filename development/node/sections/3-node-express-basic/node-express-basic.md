### Node.js - Guide
#### section 3 - Express - Basic API Development with Express

A brief outline of a starter template for developing a Node.js based API with Express &c.

#### Contents
* intro
* express setup
* express config & test routes
* express static directory
* add templates to Express
* using templates with Express
* add dynamic content with templates
* using `partials` with handlebars
* using `helpers` with handlebars
* using middleware with Express
* create some middleware for Express
* use middleware to log to a file
* create a maintenance middleware

#### intro
We can use Node.js and Express to create a custom web server for serving web applications and APIs.

We can also use Node.js to help create a web API for a local app.

The main library for this development with Node.js is `Express`.

  * [Express](https://expressjs.com/)

##### app - express setup
Create a standard project directory, and then initialise for NPM,

```
npm init
```

Then install the Express library for Node.js using NPM,

```
npm install express --save
```

Further details on Express web framework for Node.js can be found at the following URL,

  * https://expressjs.com

##### app - express config & test routes
To use Express, we usually create a JS file to store config settings and routes for the web server, e.g. `server.js`.

In this file, we can then `require` Express as expected,

```javascript
const express = require('express');
```

and start to create the server and its API routes.

e.g.

```javascript
// require express web framework
const express = require('express');

// use express
var app = express();

// specify port number for server
app.listen(3030);

/*
api routes for server
*/

// api - get route for root of server
app.get('/', (req, res) => { // req = http request, res = server response to request
  res.send('welcome to the api root...');
});
```

So, our first Express web server is available and listening on,

  * http://localhost:3030/

We've setup our app to listen on port number `3030`. We've then added our first route to the app's API, currently defaulting to root of the app.

We can also add some HTML, JSON &c. to an API route, if necessary. e.g.

```javascript
// api - get route for root of server
app.get('/', (req, res) => { // req = http request, res = server response to request
  res.send({
    title: 'test express server',
    author: 'ancientlives'
  });
});
```

We can add many other routes to our API, e.g.

```javascript
// api - test help page route
app.get('/help', (req, res) => {
  res.send('<h3>Help Route</h3>');
});
```

We then navigate to that route, e.g.

  * http://localhost:3030/help

##### app - express static directory
We can also specify a directory for serving static files &c. as a route in our server's API.

e.g. we might create a local directory, `public`, for available static files.

```javascript
// middleware - serve static files from public dir
app.use(express.static(__dirname + '/public'));
```

Express middleware `use` allows us to provision this directory for static files.

We can then store any required HTML, CSS, JS, images &c. and reference them relative to the route of the app

  * http://localhost:3030/archive.html

##### app - add templates to Express
A common option for developing dynamic web apps with Express is the addition of a *template engine*. There are many options available, including Pug (formerly *Jade*) and Handlebars. Both are popular options.

A template engine helps us render HTML in a dynamic manner, thereby injecting any required values and data as needed by an app's given context. For example, we might inject titles, usernames, the current date, a dynamic calculation, and so on.

We can also use templating to create abstracted code blocks, for example a common header or footer for a web app.

We can add *handlebars*, for example, to an Express app by installing a module for a handlebars view engine, e.g.

```
npm install hbs --save
```

Further details for this view engine are available at the following URL on NPM,

  * https://www.npmjs.com/package/hbs

This module will effectively tell Express to use *hbs* as the view engine for the current app, e.g.

```javascript
const hbs = require('hbs');

app.set('view engine', 'hbs');
```

The `set()` method allows us to *set* extra options for the way Express works with the current app. We can set various key:value pairs to define such options. For example, we simply tell Express to use the current install of *hbs* as the view engine for this app.

##### app - using templates with Express
Express will then start to check for template files in a default directory called `views`. We add this directory to the root of a project, and then start adding any required templates for the app.

e.g. we might add a template for an app's `help` definition, which will be saved in the `views` directory as `help.hbs`.

To use this template, for example, we can update the `help` api route as follows,

```javascript
// api - test help page route
app.get('/help', (req, res) => {
  res.render('help.hbs');
});
```

So, instead of `res.send()`, we can now render a template from the `views` directory.

##### app - add dynamic content with templates
We can use templates to dynamically add and update content. For example, we might update usernames, copyright information, dates, and so on.

e.g. we might add a title and dynamic year value to render in the `help` api route,

```javascript
// api - test help page route
app.get('/help', (req, res) => {
  res.render('help.hbs', {
    title: 'Helpful Information',
    year: new Date().getFullYear()
  });
});
```

We can then use these values with Handlebars syntax in the given page, e.g.

```html
...
<h3>{{title}}</h3>
<p>Copyright - {{year}}</p>
...
```

With handlebars, the double braces `{{}}` denote the injection of a defined value.

##### app - using `partials` with handlebars
Handlebars helps us create and add *partials* to our app, which allows us abstract and modularise the rendered and dynamic content. e.g. a site-wide header and footer to avoid repetition within the app.

In our `server.js` file, we can update our app's *hbs* config for Express to specify the required directory for *partials*. e.g.

```javascript
hbs.registerPartials(__dirname + '/views/partials');
```

This will set the directory relative to the absolute root of the project, which will be `/views/partials`. This is where we can save our files for app *partials*.

So, we might create a *partial* for a `footer`. We can create a new file for our footer, `footer.hbs`, and save it in the `views/partials` directory.

```html
<footer>
  <p> App designed by ... </p>
  <p>Copyright - {{year}}</p>
</footer>
```

We can then add this *partial* into any fi1e that requires a footer using the following syntax,

```html
{{> footer}}
```

If we're using `nodemon` to monitor changes to a development site, we need to add a flag for the watched files as it won't monitor `hbs` files by default, e.g.

```
nodemon server.js -e js,hbs
```

So, we now have an abstracted footer, which can be updated just once in the *partial* file.

**n.b.** If we want to use dynamic values with these partials, we need to ensure that the value is passed to the route where the partial is specified.

##### app - using `helpers` with handlebars
`helpers` with Handlebars are a useful way to specify custom functions.

For example, we might have duplicated code, calculations, and other dynamic values in routes &c. that can abstracted to a single `helper` with Handlebars.

The calculation for the current year, for example, may be abstracted to a `helper`. This means we could easily pass it to any template, partial &c. where the year is required. e.g.

```javascript
// two parameters - name of the helper function and function to run...
hbs.registerHelper('getYear', () => {
  return new Date().getFullYear();
});
```

Then, we can add it to a template, partial &c. as follows,

```html
...
{{getYear}}
...
```

Handlebars will initially check for a *helper* named `getYear`, and then it will check any explicitly passed value for this name.

So, we no longer need to explicitly pass the year value in the single route. We can just call the *helper* where required in our app.

These *helpers* can also accept parameters, so we can perform calculations &c. as needed. For example, we might use a *helper* to convert passed text to uppercase.

e.g.

```javascript
hbs.registerHelper('makeUpper', (text) => {
  return text.toUpperCase();
});
```

Then we can call this helper in a template file &c. e.g.

```html
{{makeUpper title}}
```

In this example, we're calling the *helper* `makeUpper` and passing an argument of the value `title`, which has been set a value for the current route. So, the *helper* is now modifying the passed dynamic content in the template.

##### app - using middleware with Express
Express middleware allows developers to augment and modify current Express functionality. Either add some middleware, or simply code a custom add-on for an existing app.

e.g. we might add some middleware to help authenticate a user, a session &c. Or, perhaps add some middleware to work with a request and response from an Express route.

We've already seen an example of middleware usage with

```javascript
app.use(express.static(__dirname + '/public'));
```

##### app - create some middleware for Express
To register middleware for use with Express, we need to use the following structure

```javascript
// register middleware - pass three arguments for function...
app.use((req, res, next) => {
  ...  
});
```

The `next` parameter allows us to stop the `app.use` function as and when execution, data &c. is finished for this `app.use()` function.

The middleware may act in an async manner, which means only when we call `next` will the middleware continue to execute.

So, we need to be careful how and when we use such custom middleware. If we don't call `next` in the function, any code execution after this middleware will not execute. e.g. API routes will not be registered and available, and so on. In effect, the app may simply hang and not serve any content, routes &c.

To ensure that the middleware finishes correctly, and then allows the rest of the app to execute, we call next as follows, e.g.

```javascript
app.use((req, res, next) => {
  next();
});
```

An example middleware might be a simple logger for the server usage, e.g.

```javascript
// custom middleware - a simple logger for the app
app.use((req, res, next) => {
  var now = new Date().toString();
  console.log(now);
  next();
});
```

The `req` object contains all details about the request, including everything about the http request itself, the path, http method (e.g. GET), request parameters, client details & so on.

```javascript
app.use((req, res, next) => {
  console.log(`request = ${req.method} - ${req.url}`);
});
```

##### app - use middleware to log to a file
We can also create some custom middleware to log server usage &c. to a file.

We need to require `fs` in our app,

```javascript
const fs = require('fs');
```

We can then update our custom middleware as follows to create a logger for the returned `req` object,

```javascript
// custom middleware - a simple logger for the app
app.use((req, res, next) => {
  var now = new Date().toString();
  // log details from the returned request object
  var logger = `request: method = ${req.method} & url = ${req.url}`;

  // output logger details to console
  console.log(logger);
  // save logger details to log file
  fs.appendFile('request.log', logger + '\n', (err) => {
    if (err) {
      console.log('write to log file failed...');
    }
  });
  next();
});
```

*n.b.* the third argument passed to `fs.appendFile()` is now required from Node v7 onwards...

##### app - create a maintenance middleware
A fun use of for a custom middleware is to setup a maintenance pause for the server, e.g.

```javascript
// custom middleware - a maintenance template for a server
app.use((req, res, next) => {
  res.render('maintenance.hbs'); // server paused for maintenance...
});
```

This simple update renders the hbs template to the server, and then pauses all subsequent execution until either `next()` is called or the server is restarted without the maintenance mode.

**n.b.** a big caveat for middleware usage - execution of middleware, `app.use()`, is in the order added to the server file. i.e. hierarchical execution...
