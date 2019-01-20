# Introducing promises

# Re-cap


* Error-first callbacks - what are they and why are they **neccessary** in asynchronous code ? 
* What are the problems arising from call-back functions? Mention difficulties like **callback-hell** 


# Learning Objectives


Students should: 
* Understand what promises are and why they are useful
* Understand how to use the `Promise` constructor in order to promisify async functions that take callbacks.
* Understand how to use `catch` blocks in order to handle any errors in their promise chain.
* Use static methods such as `Promise.all` which is used to resolve an array of promises : used when dealing with multiple asynchronous requests.


# Error-first callbacks

* Error-first callbacks were the standard for some time in node: however, they force us into callback hell. In order to guarantee processes running in sequence we are forced to nest callback functions.

```js

const fetchMP3 = (track,cb) => {
  setTimeout(() => {
    if (typeof track !== 'string') return cb({
      message : 'Invalid track format: Should be a string'
    })
    cb(null,`${track}.mp3`)
  },Math.random() * 6000)
}

fetchMP3(track,(err,mp3) => {
  console.log(`Here is my ${mp3}...`);
});

```

The above snippet allows us to access the MP3.
But what if we have another async function that converts an MP3 into a WAV file.

```js

const convertMP3ToWav = (MP3,cb) => {
  setTimeout(() => {
    if (MP3.slice(-4) !== 'mp3') cb({
      message : 'Invalid format: File should be an mp3'
    });
  },Math.random()* 6000)
}

const convertTrackToWav = (track,cb) => {
  fetchMP3(track,(err,MP3) => {
    convertMP3ToWav(MP3,(err,wavFile) => {
      console.log(wavFile);
    });
  });
}

```

Promises will allow us to write much neater code that looks something like this:

```js

fetchMP3('Club Tropicana')
.then(MP3 => return convertMP3ToWav(MP3))
.then(wav => {
  console.log(`Here is the wav file`)
});

```
But what is actually happening under the hood?


# What are promises ?


* A promise is an object that acts as a placeholder for the eventual result an asynchronous process.  A promise has a **state** which is initially **pending**, then when the async process has completed one of two things can happen:

* An async process is successful in which case the promise is said to be **fulfilled**
* An async process doesn't succeed, in which case the promise is said to be **rejected**

We can take the async function `fetchMP3` from earlier and re-factor it so that it returns a `Promise`.

Here is an example below : 


```js
const fetchMP3 = (track,cb) => {
  return new Promise((resolve,reject) => {
  setTimeout(() => {
      if (typeof track !== 'string') reject({
        message : 'Invalid track format: Should be a string'
      })
      resolve(`${track}.mp3`)
    }, Math.random() * 6000)
  })
}
```

The `Promise` constructor takes a function (called the executor) that takes two handlers (functions): `resolve` and `reject`.  So in order to promisify a function we wrap it in a new `Promise` and pass the successful response to the `resolve` handler and the error to the `reject` handler.

How would you promisify the following function `readFileWithPromise` method ?


```js
const readFileWithPromise = (filePath,cb) => {
    fs.readFile(filePath,'utf8',(err,fileData) => {
      if (err) cb(err)
      else cb(null,fileData);
    })
  })
}
```


# `.then()`

* Promise objects have available to them a `then` method. We pass the `then` method a callback function.  If the promise resolves then behind the scenes (this is somewhat complicated, but have faith that it does!) the callback function that is passed to `then` is passed the result of the async process.

```js

const fetchMP3 = (track,cb) => {
  return new Promise((resolve,reject) => {
    setTimeout(() => {
        if (typeof track !== 'string') reject({
          message : 'Invalid track format: Should be a string'
        })
        resolve(`${track}.mp3`)
      },Math.random() * 6000)
    })
}


fetchMP3.then(MP3 => {
  // this callback function is invoked when the promise has been fulfilled
  console.log(MP3,'Here is my MP3')
;})

```
## Important!

The `then` method will always return a `Promise` itself - it is this that allows us to build promise chains.  Because `then` returns a `Promise` we can chain async processes like this:-


```js

fetchMP3('')
.then(MP3 => return convertMP3ToWav(MP3))
.then(wav => {
  console.log(`Here is the wav file`)
})
```


We can see that the first call to `then` returns a `Promise` which means we can chain another `then` afterwards.  The functions that are passed into the `then` are guaranteed to execute in this order.


# Error-handling

* You will have seen earlier that the `reject` function is passed the error.  If something goes wrong then the `Promise` is said to have been **rejected**


* Promises allow us to deal with errors very effectively.  Consider a long `Promise` chain where anything could go wrong then if an exception is thrown or an error occurs then it will be passed to the `catch` method. 

```js

fetchMP3('')
.then(MP3 => return convertMP3ToWav(MP3))
.then(wav => {
  console.log(`Here is the wav file`)
})
.catch(err => {
  console.log('Something went wrong!')
  console.log(err);
})
```
* In the above example, there could be an error with the first async function `fetchMP3` or with `convertMP3ToWav` : in either case, if an error occurs then it will be passed to the `catch` block.



# `Promise.all`


Suppose you wanted two async operations in parallel:
```js
// Don’t do this
asyncFunc1()
.then(result1 => {
    handleSuccess({result1});
});
.catch(handleError);

asyncFunc2()
.then(result2 => {
    handleSuccess({result2});
})
.catch(handleError);
```

* Handling these two operations is problematic because they are both fired off at the same time and we would have to have our own logic to handle these processes.

* `Promise.all` is a method available on the `Promise` constructor that allows us to handle multiple async processes. Promise.all takes an array of promises and once all of them are **fulfilled** then, Promise.all will **fulfill** with an array of their values.


``` js
function fetchImage (filename) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(`${filename}.jpg`)
    }, Math.random() * 3000);
  });
}

Promise.all([fetchImage('dog'), fetchImage('cat'), fetchImage('duck')]).then(console.log)
```

With Promise.all, I can call all of these functions in an iterable array, and it will resolve with the items in an array of the same order. 

What do we think will happen if a Promise rejects in a Promise.all? (Show failure)



# Common mistakes


## Nesting promises

```js
// Bad news...
asyncFunc1()
.then(result1 => {
    asyncFunc2()
    .then(result2 => {
        ···
    });
});
```
In the above example, we are needlessly nesting the promises :
instead, we can just do the following:-

```js

asyncFunc1()
.then(result1 => {
    return asyncFunc2();
})
.then(result2 => {
    ···
});

```
Now we don't need to nest therefore avoiding callback hell! 
Hurrah - our code becomes way neater and readable.


## Returning promises instead of chaining

```js

// Don’t do this
const insertInto = (db) =>{
        return new Promise((resolve, reject) => {
          db.insert(this.fields)
          .then(resultCode => {
              this.notifyObservers({event: 'created', model: this});
              resolve(resultCode);
          }).catch(err => {
              reject(err);
          })
        });
    }
```


```js

const insertInto = (db) => {
        return db.insert(this.fields)
        .then(resultCode => {
            this.notifyObservers({event: 'created', model: this});
            return resultCode;
        });
    }
}
```