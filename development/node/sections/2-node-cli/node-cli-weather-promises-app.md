### Node.js - Guide
#### section 2 - cli - `node-weather` test app with Promises

A brief example async weather app for Node.js command line development and usage.

#### Contents
* intro to Promises
  * use instead of callback wrappers
* `node-weather` app - part 2 intro
* update promise usage

#### intro to Promises
ES6 *promises* have been designed to manage async requests, and handling of their return. Promises often replace callback structures, including multiple queries to DBs, APIs, and so on.

We can also chain *promises* to stagger execution or stage multiple queries from a to b to c, and so on.

##### use instead of callback wrappers
The callback examples we've just seen for the test weather app can now be updated to use *promises*. We can also wrap the `request` module in a *promise* in spite of this modules lack of native support for *promises*.

e.g.

```js
// require request module
const request = require('request');

// get address from geocode api
var getAddress = (address) => {
    // return promise object - use promise constructor
    return new Promise((resolve, reject) => {
      /*
        define request to remote api - copied from v0.7 node-weather
      */
      // encode input from yargs to console
      const encodeAddress = encodeURIComponent(address);

      // call request function - pass options object and callback function for return...
      request({
          // set url for api query - append encoded user input address
          url: `https://maps.googleapis.com/maps/api/geocode/json?address=${encodeAddress}`,
          json: true
      }, (error, response, body) => {
        // add initial error handling for query and return object
        if (error) {
          // error object for query - call reject due to Promise
          reject('could not connect to remote servers...');
        } else if (body.status === 'ZERO_RESULTS') { // specific to Google API - check return property for other APIs
          // checks return status code for result - e.g. no results found - again, call reject due to Promise
          reject('Unable to find requested address...');
        } else if (body.status === 'OK') {// return comes back as OK...
          // call resolve due to Promise - return object with geocode data
          // undefined not needed - resolve and reject one parameter...
          resolve({
              address: body.results[0].formatted_address,
              locationType: body.results[0].geometry.location_type,
              latitude: body.results[0].geometry.location.lat,
              longitude: body.results[0].geometry.location.lng
          });
        }
      });

    })
};

getAddress('60601').then((location) => {
    console.log(location);
}, (error) => {
    console.log(error);
})
```

#### `node-weather` app - part 2 intro
This second part of the weather app uses *promises* to wrap `request` usage for query and data handling within the app.

#### update promise usage
The `app.js` file can be used to set and manage the logic for the app's execution - minimum code to abstract and execute the app.

So, we can set the options for `yargs`, which can be used to manage input at the CLI for the app.

Then, we can add the calls to the geocode and weather functions, which return the required promises, e.g.

```js
/*
Geocode and Weather
 - get Promise for geocode location first
 - then use address to get weather
*/
// call get Address first, and then use return data in promise to get weather from api...
geocode.getAddress(argv.address).then((res) => {
  console.log("Geocode Promise returned...");
  console.log('Geocode: ', res);
  //get weather data - pass data from geocode promise
  return weather.getWeather(res.latitude, res.longitude);
}).then((res) => {
  console.log("Weather Promise returned...");
  console.log('Weather:', res);
})
.catch((errorOutput) => {
  console.log(errorOutput);
});
```

These promises are returned from functions defined in abstracted modules for geocode and weather, in their respective files. This adheres to node.js usage to abstract logic and usage to separate modules.

e.g. the weather module returns a promise,

```js
// Promise - get address from geocode api & return promise object
var getWeather = (lat, lng) => {
  // return promise object - use promise constructor
  return new Promise((resolve, reject) => {
    // request - options parameter and callback for return data
    request({
        url: `https://api.darksky.net/forecast/047579dad78e5c91a4f468c95380a92f/${lat},${lng}`,
        json: true // request parses body as json...
    }, (error, response, body) => {
        if (error) {
            //console.log('unable to connect to weather api...');
            reject('unable to connect to weather api...');
        } else if (response.statusCode === 404) {
            //console.log('unable to fetch weather forecast...');
            reject('unable to fetch weather forecast...');
        } else if (response.statusCode === 200){
            //console.log(body.currently.temperature);
            resolve({
                temperature: body.currently.temperature,
                apparentTemp: body.currently.apparentTemperature
            });
        }
      });
  });
};
```

We simply call this function with the expected parameters in `app.js`, and then handle the return promise object.

The same pattern may be used for the custom geocode module.
