### Node.js - Guide
#### section 2 - cli - `basic-async` test app

A brief example app for Node.js command line development and usage.

#### Contents
* Intro
* local aysnc
  * basic non-blocking
  * call stack
  * event loop

#### intro
Even with local examples, we can see how the asynchronous model works for a basic app.

In the standard synchronous model, if we add a couple of `console.log()` statements at the start and end of our app's logic, they will always execute in the expected order.

#### local async - basic non-blocking
To test the basic concept of non-blocking asynchronous code, we can add a simple timeout to the app's initial code, e.g.

```js
// accepts a callback and specified time delay
setTimeout(() => {
    console.log('output from set timeout...');
}, 2500);
```

This basic asynchronous logic accepts a callback and a time delay for the timeout itself. So, it will wait 2.5 seconds, and then output to the console the provided message.

Now, the async part is that we are not telling Node.js to wait 2.5 seconds. Instead, we are telling Node.js to fire the callback in 2.5 seconds. This means that the app can continue to work whilst the 2.5 seconds is counted down. Then, Node.js will output the message to the console.

So, in this example, we get the expected message for the start of the app output to the console, and then the next executable logic. After 2.5 seconds has elapsed the specified message will be output to the console. This means that the order of the output messages will not necessarily match the expected order of execution in the app's code. Instead, we'll get the following

```
app starting...
app stopping...
output from set timeout...
```

The async part of the app's code does not block the overall execution of the app.

#### local async - call stack
As we start to consider how async execution works, it's helpful to consider the original synchronous execution within the call stack of an app.

The *call stack* in V8 works as expected, as a standard stack data structure. Add items to the top, and then remove those items and work down to the last item at the bottom. A stack of plates to wash and clean.

For a standard synchronous app, we might have the following basic code, e.g.

```js
var a = 10;
var b = 20;
console.log(`a is ${a} and b is ${b}`);
```

When the app starts the `main()` will be added to the bottom of the call stack. Then, as it executes each statement will be added and removed, as necessary, until the app has finished. Then the `main()` method is removed, and the call stack is empty.

So,

  * statement to set variable `a` to 10 is run
    * then removed from call stack
  * then the next statement is run, setting the value of variable `b` to 20
    * then removed from call stack
  * then, the final statement is run - outputs log to the console
    * again, removed from call stack when finished executing
  * `main()` is then removed from the bottom of the call stack

*n.b.* interesting difference between how classic and async code is executed in the same app...

For an async app, the execution will use more than just the call stack. For Node.js apps, we have the following

```

   ------------             -----------
  | call stack |    -->    | Node APIs |
   ------------             -----------
                                 |
              ^ -->              |
   event loop |   |              |
              <-- v              |
                                 v
   ------------------------------------
  |          callback queue            |
   ------------------------------------

```

For a non-blocking example, which uses the basic timeouts, e.g.

```js
// initial app loading output to console
console.log('app starting...');

// accepts a callback and specified time delay
setTimeout(() => {
    console.log('output from first set timeout...2.5 seconds delay');
}, 2500);

// accepts a callback and specified time delay
setTimeout(() => {
    console.log('output from second set timeout...0 seconds delay');
}, 0);

// app stopping output to console
console.log('app stopping...')
```

The execution of this app starts as expected. The first statement, `console.log`, will be added to the call stack, executed, and then removed from the call stack. The app then moves on to the second statement. `setTimeout` is a Node API, it is not provided by V8 itself. Instead, Node.js gives us access to this API. So, as it executes, it is added to the call stack as expected, but it will also be added to the Node APIs currently executing. It will execute, which means it starts the timer for 2.5 seconds.

However, this is where the non-blocking part works. Just because the timer is counting down does not mean the call stack cannot continue to execute available statements in the app. So, it will now execute the next `setTimeout` function, which will follow the same pattern.

Then, the call stack will execute the final statement, which is another `console.log()`. As this function is a standard JS function, its execution and output will run immediately. So, it outputs the text to the console.

#### local async - event loop
The interesting part of this execution is when the timers finish for each Node API `setTimeout`. When they finish they will not execute and output immediately. Instead, they move their output &c. down to the callback queue.

This callback queue contains all of the callbacks that are ready to be fired. So, each callback function is now waiting for the call stack to be empty. When the call stack is empty, each waiting callback function may now be executed. This is the job of the *Event Loop*. It monitors the call stack, and executes any available callback functions when the call stack is empty. Effectively, it moves each available callback function onto the empty call stack. This is where the callback functions will be executed.
