### Node.js - Guide
#### section 2 - cli - `node-weather` test app

A brief example async weather app for Node.js command line development and usage.

#### Contents
* Intro
* async query - geocode api
* app - getting started
  * make request to api
  * &c. - pretty printing
  * working with http request
  * user input with yargs
  * encode/decode user input strings
  * handling async callback errors
  * refactor basic logic
  * refactoring callback logic &c.
  * add static weather api call
  * chaining callbacks for geocode and weather

#### intro
A simple app to request the weather forecast and conditions for a specified location. These requests are fetched from a remote API using asynchronous logic.

Remote location query uses Google's Maps geocode API.

#### async query - geocode api
Using an async process, we can query Google's APIs for geocode data for a specified location.

e.g. we can query the following type of API url,

  * https://maps.googleapis.com/maps/api/geocode/json

and then specify the location as query strings to this url. These are `key:value` pairs for the location, e.g.

  * https://maps.googleapis.com/maps/api/geocode/json?address=brixham

If we want to use a more specific address with this type of url, the format may be as follows for testing,

  * https://maps.googleapis.com/maps/api/geocode/json?address=3%20the%20strand%20brixham

This URL query will return some JSON with metadata for the requested address, including postal code, latitude and longitude, and so on. Lots of geocode data is returned for this simple initial query.

In Chrome's browser, we can also add a useful extension called *JSONView*, which makes it easier to view and interact with the return JSON in the browser.

#### app - getting started
We'll start our basic weather app by issuing the standard call to setup a Node app,

```
npm init
```

This will create the basic `package.json` for our app with the specified metadata. We can then start to install any required NPM packages, and build out the basic app.

The first package we'll need is *request*, and we can install it using the standard NPM command,

```
npm install request --save
```

Again, we'll add the `--save` flag to ensure this package is set as a dependency in the app's `package.json` metadata.

##### app - make request to api
We can now create our `request` query to the remote api for the geocode data.

As expected, we'll start by requiring the `request` module,

```js
const request = require('request');
```

Then, we can add and setup our request function. This accepts two parameters, which include the following,

  * options object for the request function
    * unique url for the request
    * json boolean to ensure that json is returned for this query
  * a callback function - action once the requested data has been returned to the function

e.g.

```js
request({
    url: 'https://maps.googleapis.com/maps/api/geocode/json?address=brixham',
    json: true
}, (error, response, body) => {
  console.log(body);
});
```

The parameters for the callback function are mentioned in the docs for the `request` module itself. So, we're simply adhering to the format specified in the docs.

For the callback execution, we can simply log to the console the return body data. (we can update this functionality later...).

Then, simply test this initial app as usual,

```
node app.js
```

This will submit the query to Google's Geocode API, and then return the expected results for the address.

##### &c. - pretty printing
A quick note on improving the output of a JSON object, in effect so we can read it easily at the terminal.

```
console.log(JSON.stringify(body));
```

So, we'll see a full output for an object &c.

We can also add some additional options to customise the output, e.g.

```
console.log(JSON.stringify(body, undefined, 3));
```

First argument is the object to stringify, the second is the option to ignore specified properties, and the third is number of spaces per indentation in the formatted string output, e.g. 3.

##### app - working with http request
There are three arguments that we provide for the `request` itself, including `(error, response, body)`

  * body - this comes from HTTP itself, as expected
    * return is the `body` of the data, e.g. body of HTML page, body of JSON...
    * in this request, it's the main return data for the location request
  * response - includes everything coming back from the http request
    * e.g. status code (200 for OK) - very useful for checking return, fetch &c.
    * body of the request
    * headers - comes from http request as key:value pairs
        * content-type, date, expires, server, &c.
        * header set by Google for this particular API request
        * headers may also be set for the original query request, e.g. a request for JSON from the remote server...
  * error - error for the http request itself
    * e.g. can't make connection, query issues, domain doesn't exist, and so on...
    * error code returned from request - system errors...
      * useful for error handling
      * use values in error object to determine error handling to use...

##### app - user input with yargs
Our app needs to accept user input for an address &c. at the command line. This type of input will naturally be natural, plain text, which might include spaces, hyphens, &c. e.g.

```
input requested address: 3, the strand, brixham, england
```

So, we need to encode this input to make it compatible with the required string for the request url to the remote api.

To start with, we need to setup `yargs` so our app can accept user input. We can start by installing yargs from npm, e.g.

```
npm install yargs --save
```

Then, we can require the yargs module for user in our app,

```js
const yargs = require('yargs');
```

The app only requires a single command at the moment, so we can set options for yargs to a default options, e.g.

```js
const argv = yargs.options({
    adr: {
        demand: true,
        alias: 'address',
        describe: 'address for weather app query',
        string: true
    }
});
```

We're specifying some options for the address user input, including the standard demand, alias, and describe. We've also set a new option, `string`, to ensure that the processed address input is defined as a string type.

Of course, as we're using yargs, we can also add `help()` for the user input, and then set an alias for help as well, if needed, e.g.

```js
...
.help()
.alias('help', 'h')
```

If a user now issues a command for `--help`, a description and command alias and full option will be printed to the CLI.

If we run the app with a simple address string, e.g.

```
node app.js --adr="3 the strand brixham"
```

The Google Geocode API will do its best to find the requested address, which will then allow the app to print the defined console output specified in the `request` callback.

  * formatted address
  * location type
  * latitude
  * longitude

We can also check the object returned by yargs itself, e.g.

```js
// output input from yargs to console
console.log(argv);
```

##### app - encode/decode user input strings
For encoding a string, we can use the `encodeURIComponent()` method, which accepts a single argument for the string to encode. e.g.

```js
encodeURIComponent('3 the strand brixham')
```

This method will then return an encoded string, which will match the string we see if we enter that address, for example, as part of the query URL to Google's API, e.g.

```
3%20the%20strand%20brixham
```

We can then add this encoded string to the query URL for the API in our app's code, and the query will now be formatted correctly.

This method will encode various characters, and not just spaces, e.g.

```js
encodeURIComponent('test, string')
'test%2C%20string'
```

The `,` becomes `%2C`, and the `space` becomes the expected `%20` encoding.

We can also decode these encoded strings using the reverse method, `decodeURIComponent`. As expected, if we pass the encoded string to this method, it will simply decode to the full string, e.g.

```js
decodeURIComponent('test%20string')
'test string'
```

So, we can start adding these methods, as required, within our weather app.

##### app - handling async callback errors
As we submit requests to an api, invariably we'll encounter instances where errors will be returned. We need to handle them in a manner that makes sense to both the app's logic and a user.

For example, if we currently try to fetch an address that doesn't exist, or perhaps contains errors or typos, the callback will throw an error, and the app will crash.

We need to add a series of `if-else` conditional statements to the callback to handle some initial errors, e.g. return status codes are a useful initial check.

So, we can initially check a couple of possible areas where errors can arise - the local machine errors, and remote return query errors

```js
if (error) {
  // in error object for query
  console.log("could not connect to remote servers...");
} else if (body.status === 'ZERO_RESULTS') { // specific to Google API - check return property for other APIs
  // checks return status code for result - e.g. no results found
  console.log('Unable to find requested address...');
} else if (body.status === 'OK') {// return comes back as OK...
  // output formatted address for query
  console.log(`Requested Address: ${body.results[0].formatted_address}`);
  // output location type
  console.log(`Location type: ${body.results[0].geometry.location_type}`);
  // output location latitude
  console.log(`Latitude: ${body.results[0].geometry.location.lat}`);
  // output location longitude
  console.log(`Longitude: ${body.results[0].geometry.location.lng}`);
}
```

So, if we now request an invalid address, e.g.

```
node app.js --adr 00000
```

the error will be handled correctly, and as expected - simply informing the user `Unable to find requested address...`.

Likewise, we can check errors for an error to connect to the remote server (e.g. bad URL, no network connection &c.), and then finally output the expected results for a successful query and return object.

##### app - refactor basic logic
We can now start to refactor our app to move the geocode related logic to a separate file.

So, we'll now create a new directory and file for the separate geocode location logic, e.g.

```
./geocode/geocode.js
```

Then, we can move the `require` statement for request to this new file, and create a new function to get the location object for the requested address. Effectively, we're now moving the logic for the address query from `app.js` to `geocode.js`.

```js
// get location details for requested address - accepts plain address...
var addressLocation = (address) => {
  ...
};
```

We can then tidy up the `app.js` file, and call this new function with the argument for the requested address, e.g.

```js
// get location address - pass user input address
geocode.addressLocation(argv.address);
```

##### app - refactoring callback logic &c.
As we refactor the logic for callbacks, our simple initial goal is to abstract use of the calls as much as possible. Effectively, we're again trying to avoid - DRY - in the app's logic.

So, we can now update our call to `addressLocation()` in app.js to accept a function. This function is the second parameter, and it will be called after the return comes back for the `addressLocation` function. We can also set two expected parameters for this arrow function as well, e.g.

```js
geocode.addressLocation(argv.address, (errorOutput, addressResults) => {

});
```

The two parameters basically allow us to either use the return error output or the return results for the address query. As we know this will happen for each address query, this abstraction helps simplify our code for multiple potential queries.

The other obvious benefit to this DRY approach is that we now have a simple way to determine the success or failure of the request. It's either going to be a return of `errorOutput` or `addressResults`.

So, we can now simply output the `errorOutput` if the return is an error instead of checking and manipulating the error object for the request. That error object manipulation has been abstracted to the `geocode.js` file, e.g.

```js
geocode.addressLocation(argv.address, (errorOutput, addressResults) => {
    if (errorOutput) {
        console.log(errorOutput);
    }
});
```

This means we can now update the logic in the `geocode.js` file as well. So, in the `addressLocation()` function, we can now add the `callback` we just created in `app.js`. This is set as the second expected parameter for this function, e.g.

```js
// get location details for requested address - accepts plain address...
var addressLocation = (address, callback) => {
    // then use the callback for an error object return to app.js
    ...
    if (error) {
        callback('could not connect to remote servers...');
    } else if (body.status === 'ZERO_RESULTS') {
        callback('Unable to find requested address...');
    } else if (body.status === 'OK') {
         callback(undefined, {
            address: body.results[0].formatted_address,
            ...
        });
    }
};
```

We now get a return object for the required location data. This can be easily updated in the `geocode.js` file, which will then provide additional data in the return object. e.g.

```js
{ address: 'Chicago, IL 60601, USA',
  locationType: 'APPROXIMATE',
  latitude: 41.8839927,
  longitude: -87.61970559999999 }
```

##### app - add static weather api call
Sample weather API includes,

  * forecast.io - developer.forecast.io (1000 free requests per day)
    * known as the *Dark Sky API*

Sample API call is as follows,

  * https://api.darksky.net/forecast/..api_key.../...lat...,...lng...

We can then use this URL with an API key and required latitude and longitude to query the API itself using the `request` module, e.g.

```js
// DarkSky API - 047579dad78e5c91a4f468c95380a92f
const request = require('request');

// request - options parameter and callback for return data
request({
    url: 'https://api.darksky.net/forecast/047579dad78e5c91a4f468c95380a92f/37.8267,-122.4233',
    json: true // request parses body as json...
}, (error, response, body) => {
    console.log(body.currently.temperature);
});
```

From the return API data, we've selected the temperature property for testing.

We can also add some basic error handling for the request, as we saw with the geocode query, e.g.

```js
...
if (error) {
    console.log('unable to connect to weather api...');
} else if (response.statusCode === 400) {
    console.log('unable to fetch weather forecast...');
} else if (response.statusCode === 200){
    console.log(body.currently.temperature);
}
...
```

##### app - chaining callbacks for geocode and weather
We can now create a new folder and file for our weather api query and handling, e.g. `weather/weather.js`

Then, we can move the logic for querying the weather api, and handling the return data to this new file from our current `app.js` file, adding it to a new function to get the weather,

```js
// DarkSky API
const request = require('request');

var getWeather = () => {
// request - options parameter and callback for return data
request({
    url: 'https://api.darksky.net/forecast/047579dad78e5c91a4f468c95380a92f/37.8267,-122.4233',
    json: true // request parses body as json...
}, (error, response, body) => {
    if (error) {
        console.log('unable to connect to weather api...');
    } else if (response.statusCode === 400) {
        console.log('unable to fetch weather forecast...');
    } else if (response.statusCode === 200){
        console.log(body.currently.temperature);
    }
});
};
```

We can then export it for use in the app itself. e.g.

```js
// export the api for the module
module.exports = {
    getWeather
};
```

In `app.js`, we need to re-enable the code for querying Google's geocode api, then `require` the new weather file, and then setup `app.js` to query and handle the weather api.

As with the geocode call in `app.js`, we need to setup some properties for the location coordinates, and a callback to handle the return data from the weather api, e.g.

```js
// get weather details - pass lat, long, and a callback
weather.getWeather(lat, lng, (errorOutput, weatherResults) => {
    if (errorOutput) {
        console.log(errorOutput);
    } else {
        console.log(weatherResults);
    }
});
```

We can then update the `getWeather()` function in `weather.js` to handle the passed properties, including the callback e.g.

```js
var getWeather = (lat, lng, callback) => {
// request - options parameter and callback for return data
request({
    url: `https://api.darksky.net/forecast/047579dad78e5c91a4f468c95380a92f/${lat},${lng}`,
    json: true // request parses body as json...
}, (error, response, body) => {
    if (error) {
        //console.log('unable to connect to weather api...');
        callback('unable to connect to weather api...');
    } else if (response.statusCode === 400) {
        //console.log('unable to fetch weather forecast...');
        callback('unable to fetch weather forecast...');
    } else if (response.statusCode === 200){
        //console.log(body.currently.temperature);
        callback(undefined, {
            temperature: body.currently.temperature,
            apparentTemp: body.currently.apparentTemperature
        });
    }
  });
};
```

This will now output to the console the current and apparent temperature for the specified latitude and longitude.

However, the coordinates are still fixed. So, we need to setup our app to pass the returned latitude and longitude coordinates for the requested address to our weather api query.

For our initial test, we can simply move the call to the `getWeather()` function into `geocode.addressLocation()`, e.g.

```js
// get location address - pass user input address
geocode.addressLocation(argv.address, (errorOutput, addressResults) => {
    if (errorOutput) {
        console.log(errorOutput);
    } else {
        // output the requested formatted address
        console.log(addressResults.address);
        // get weather details - pass lat, long, and a callback
        weather.getWeather(addressResults.latitude, addressResults.longitude, (errorOutput, weatherResults) => {
            if (errorOutput) {
                console.log(errorOutput);
            } else {
                console.log(`Current temperature is ${weatherResults.temperature}`);
                console.log(`However, temperature feels like ${weatherResults.apparentTemp}`);
            }
        });
    }
});
```

This allows us to now pass a requested address, and return the formatted address and temperature results.
