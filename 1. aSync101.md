# ASYNC101

> How many things can I do in Javascript at once?
> One. Why? Because it's single threaded.

As we've understood with our examples so far, that means doing one line of code at a time.

But we've reached the limits of this linear, synchronous way of thinking about things.

For example, what if I was building a portfolio page that displayed some of my tweets. I sent off a request to fetch my tweets, and wanted to do something with that information when it came back?

Well, that's going to take time. Make a request to Twitter's API is not instantaneous. So we have a problem. If we followed the normal, linear way of following code that we have so far, how could we stop the functionality for displaying the tweets from firing before we have the tweets back?

So if we were to plan code which will pause for 5 seconds then do something else how could we go about that

```js
const startTime = Date.now();
while (Date.now() < startTime + 5000) {
  // do nothing
}

console.log('doing stuff now');
```

This approach is blocking code the while loop blocks everything running in our single thread until its complete

why is blocking bad? takes a while to load...
say you were loading a website with images that might take a while to load... if you cant do anything else until everything has loaded it will be super slow

this is where async comes in: change our single linear way of thinking so we can avoid bad code like this...

## Sources of asyncronicity

There are 5 main sources of asyncronous code;

1. User interactions
2. Network I/O communicate between devices on a network
3. Disk I/O read write time
4. IPC - Interprocess communication i.e. web workers
5. Timers - we'll be using these to mock asychronisity

```js
function logMeFirst() {
  console.log('me first!');
}
setTimeout(logMeFirst, 1000);
// will wait for one second then run the function which logs the string 'me first'
```

Currently we understand our code with:

- The thread of execution
- The VE
- The Call Stack

In order to understand async code to the same degree, we're going to ahve to add some new tools to our kit. They are:

- Browser / Node background threads
- Callback / Message / Task Queue
- The event loop

## Background threads

- The function we have passed our timer - in this example `logMeFirst`, is stored against that timer. When the timer expires, it pushes logMeFirst to the call stack. Simple, right?
- Well hang on. What happening when my timer is set to 0 seconds? How does Javascript work out what gets called first? Is it just luck?
- When we trigger a timer, something in the background of JS, called the event loop, waits for it to complete. When it does, it pushes it onto the task queue. In this example, it completes instantly and gets pushed onto the task queue.
- But Javascript ruins to completion. So in this example, the timer completes instantly. And the function `logMeFirst` is added to the Task Queue. Now the task queue doesn't pass to the call stack until it's completely empty. Including having global popped off.
- So even though the timer is set to zero, and immediately triggers, it still has to wait in the task queue until everything else has finished. So if I called a thousand functions in the rest of the global, that took ten minutes to run, that 0 second timer would still fire afterwards.

## A more practical example

Lets think about getting something back out of these functions

// synchronous thinking

```js
const getmp3 = song => `${song}.mp3`;

const ninetiesClassic = getmp3('Wanna Be');
console.log(ninetiesClassic);
```

// asynchronous thinking

```js
const getMP3 = song => {
  setTimeout(() => {
    return `${song}.mp3`;
  }, 5000);
};

const ninetiesClassic = getmp3('Wanna Be');
console.log(ninetiesClassic);
/// undefined
```

> If you see a function with undefined what does it mean? not returning anything...but it is returning something? NO: thats the inner function passed to set timeout not the getmp3 function
> So how can we get anything "out" of an asynchronous function?

## What are callbacks?

Callbacks are functions that are pieces of code that may be executed at some point in the future. Not all callbacks are async, for example, your forEach functions in lowbar all took a callback but due to the nature of the code, it worked syncronously.

Callbacks give us a way to be able to use asyncronous processes and then continue to work on the results after the function has finished. You might notice here that we don't return anything here. That's because the return value is actually irrelevant. We can only use it in our callback.

Lets have another look at our getMP3 function. We can pass a callback function as the argument, and display the song when our asynchronous process resolves in the event loop, and gets pushed to the call stack.

```js
const getMP3 = (song, handleSong) => {
  setTimeout(() => {
    handleSong(`${song}.mp3`);
  }, 5000);
};

const logThatSong = mp3 => console.log(`${mp3}`);

getMP3('Wanna Be', logThatSong);
```

In this case logThatSong is a callback function: a piece of functionality we want to defer until we've got what it is we want...

- the key thing of saving variables no longer works

## Error-first callbacks

The convention for asynchronous callbacks is to write them error first. This is in order to ensure uniformity across async callbacks,and ensure that errors aren't forgotten.

Don't forget with setTimeout we're merely simulating a server call here.

```js
const getMp3 = (song, handleSong) => {
  setTimeout(() => {
    if (song === 'Wanna Be') handleSong({ msg: 'You disgust me' });
    else handleSong(null, `${song}.mp3`);
  });
};

getMp3('Wanna Be', (err, mp3) => {
  if (err) console.log(err);
  // don't charge customer if there's an error
  else console.log(`${mp3}`); // charge customer if goes well
});
```

You may have noticed that first answer is null. That is because, especially in node, it is convention for the first argument of the callback to be an error, if present. This allows us to pass errors and data back from async functions. It also gives us a standard way to communicate the data so that we can design our systems to act similarly.

What would have happened if our anonymous callback followed the convention but the anonymous setTimeOut function didnt? when there was a successful result like in the else, mp3 wouldnt exist because it was passed only one parameter -> so mp3 would be `undefined`

## Top tips!

## callback declaration:

- err, data

## callback invocation:

where you say what the error is going to be

- null, actual data / result
  or
- an actual error object

## Testing

Mocha uses done which is a function that is passed in as an argument to the it callback function. Mocha runs test automatically as if it was sync and by using `done`, it tells mocha that your code is async and therefore makes it wait for a response. It will wait for a certain amount of time but it also has a default timeout period, so if you get this error that it's timed out and your pretty certain your code should work, have a google about this and see what you find (i.e. you may need to make the timeout period longer)
If the error is saying `done` was called twice / more than once, you may have invoked your final callback at the wrong time so have another look at your solution
