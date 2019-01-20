# Express 101

## Recap

* Ask the students : what is the definiton of a **server**?
* Re-cap the main HTTP methods from last week.

A server is a computer used to process requests and deliver data over a local network or the internet.

## Learning Objectives

Students should

* Be able to create an express server
* Be able to use basic express setup and best-practices for file naming e.g app.js and index.js
  - also brief introdction to nodemon and package.json scripts
* Be able to understand what basic MVC architecture is.
* Understand what body-parser does
* Understand what a **parametric endpoint** is
* Extract information from urls using `req.params` and `req.query`

## Re-factoring from http into express

The servers we have been building so far have been serving from localhost. So in simple terms, when we use our browser to access localhost, localhost (our computer) deals with the request and serves up a response to our browser.

### What is REST?

So first a revision on what a restful api structure is now that we're making them:
Which of these is a restful api?

**/fetch-ponies**  *or*
**/ponies**

The idea behind RESTful routing is that I could just make ponies available, and depending on the HTTP Method (verb) I use, I can do all sorts of things with my ponies. So instead of having /fetch-ponies and /add-ponies I can have /ponies, with a GET and a POST method available in my routes.

## Introducing Express

Today we're going to introduce Express. What is it? On it's own terms, it's a 'minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.'

In simple terms, it does everything your HTTP Servers from node were doing, but it's a lot nicer to work with.

Let's look at a simple example.
How would we set up a server and listener for requests last week:

```js
const http = require('http');
const server = http.createServer((req, res) => {});
server.listen(9090, () => {
  console.log(`listening on 9090...`);
});
```

We can use the following boilerplate code to setup our app which will be an instance of the express library:

```js
const express = require('express');
const app = express();
```

which we can refactor even neater into 
```js

const app = require('express')()
```

Notice that we're not going to be passing a function with req and res to our server here: thats part of what express does, it will by default pass many of these arguments for us.


In order to ensure that our http server was actually listening for incoming requests from the client side we needed to invoke the `listen` method and also specify the port our server is listening on...

```js
server.listen(9090);

```
In express, the listen method is equivalent to the listen method used in `http.createServer`.

```js

app.listen(9090,() => {
  console.log(`Server is listening on port 9090...`)
})

```

The listen method binds the app to the relevant port so it can listen for requests from , in this example, port 9090;


Now I'm going to refactor this a little in line with best practices and actually pull out this listen function into listen.js
If listen depends on app what do we need to do?
  - export and require in app

# nodemon 

Okay, now I'm ready to run my server I think and get that console.log. 

### Q: Can anyone have a guess as to which file I should be running ? 
### A: listen.js because listen will require in app, read the contents and therefore run them, resolve back to listen and bind the now instansiated app to the port which will be listening... if we only ran app.js we wouldn't get the listen function happening.
In the previous sprint, we ran **node listen.js** and then if we made changes we had to kill the process and then save and re-start it up again.  This is tedious and unnecessary.  Instead we can use an npm package called **nodemon**.  nodemon will ensure that the server gets restarted any time a file is updated (i.e. any time a file is saved).

We can use the command `npm i -D nodemon`.  `-D` means that the package will be saved as a deveoloper dependency  - a dependency that only the developer will need to use whilst building the application.  A user should never have any need to use 
**nodemon**!

So lets run our server 
```js
nodemon listen.js
```

Now we're getting into deeper development territory we dont really want to be typing in the command nodemon listen.js its a bit annoying so we're going to start using npm scripts as well:

## NPM Scripts
 We use scripts to automate repetitive tasks.
 Most of our work will happen in the package.json file that NPM
 This is the file that is created when you run npm init.
 Here’s a sample package.json file:
```js
{   
    "name": "super-cool-package",   
    "version": "1.0.0",   
    "scripts": {    
        ...   
    }, 
    "dependencies": { 
        ...
    }    
    "devDependencies": {     
        ...   
    } 
}
```
Notice the scripts object in the file. This is where our NPM scripts will go. NPM scripts are written as usual JSON key-value pairs where the key is the name of the script and the value contains the script you want to execute.

A common one you will have seen up til now in your sprints if you've had a nosy is 
```json
"scripts": {    
      "test": "mocha spec"   
    }
```

which means when you run 
```js
npm test
```
it will run the spec directory with the mocha npm package.

So today once you've had a go at manually doing nodemon index.js a few times and got bored of it: 

how could we construct a script to do it for us?
```json
"scripts": {    
      "dev": "nodemon listen.js"   
    }
```

the command for this would be
```js
npm run dev
```

because dev isn't a normal defined script like test

[more on npm scripts](https://medium.freecodecamp.org/introduction-to-npm-scripts-1dbb2ae01633)

## Re-factoring our end-points (Basic Routing)

Before we handled different endpoints in our http server with the following kind of logic:
```js
if (req.url === '/cats') {
  if (req.method === 'GET') {
    // JS code in here...
  }
}

```

This is obviously cumbersome: in express we can use methods that will mean particular functions that we write get invoked when we hit a particular route: 
E.g. we could use something like this 


```js

const app  = require('express')();

app.get('/cats',getCats);
```

The function `getCats` is passed as an argument to the get method, which is part of the express app. 

The first argument we pass is the path `/cats`. Thus any time we get a GET request on the cats route this will trigger the second parameter which is our getCats function.  

In the usual way, getCats will be a function that it will have access to the request and response object. But note: are we passing it req and res? No express passes req and res on automatically this automatically.

 In`getCats` we can do the logic that gets cats from our database.

In addition to the string `/cats` we could also pass in regular expressions.


## Simplifying our code

```js

const getHomePage = (req, res) => {
  // setHeader allows you to set individual headers as key value pairs
  res.setHeader('Content-Type', 'text/json');
  res.statusCode = 200;
  res.write(JSON.stringify({msg : "Welcome"}));
  res.end();
};

```

The above method will update the headers in the request objects in order to inform the browser that it is being sent json.  The status code is updated to 200 for a successful response.  An actual message is written and the response sent back to the client.

In express, we can be far more succinct:
```js

const getHomePage = (req,res) => {
  res.status(200).json({msg : "Welcome"});
}

```

In the above example, we used `res.json`, this method sets the headers so that content-type is `application/json`
We could use Instead res.send sets the headers which reads the type you are sending at sets appropriate json or text/xml headers.

## Parametric end-points (variables)

So one thing we can do with Express is completely abstract the logic of our response from the server itself. But first let's add some more functionality. I'll add in a route that uses params.

Lets say we wanted to send a tailored message to a user based on the url how would you have done that before? something quite convoluted like:

```js
if (req.url.includes('tweets') {
  if (req.method === 'GET') {
    const username = req.url.split('/tweets/')[1];
    res.write(JSON.stringify({message: `hi, ${username}}`))
  }
}
```
now we can pass in a variable value into our path marked by the :
the name we give to the variable in the path will be the key in our params object and the actual variable username the client has sent will be the value.

```javascript
app.get('/cats/:catname', (req, res) => {
  res.json({msg:`Hello ${req.params.catname}!`});
});
```
This example could (and should) have all the controller function logic abstracted away, and put in a separate file called controllers. Why? Well because controllers can get pretty unwieldy. You want to be able to clearly identify what is going on in your server and what routes it has.

# Dealing with the body

Lets say we want to do a post request. How would I expect to do a post request? Let's say I wanted to post a json object with a name property through postman to get my response. With the same logic we'd like it to be on the req object under body but if you console.log it - its undefined

So, this doesn't work. Why? What problem did we encounter when we were receiving information on our request: streams of data which we had to handle the request body by assembling the body from packets that were sent over from the client. 

 Express was designed - with minimal functionality stripping out anything that was not crucial to its use. Unfortunately, that included the ability to parse the body of a post request. Obviously most websites only work with get requests, so it isn't necessary for the majority of routers. 


 So to deal with a body coming in we're going to need a bit of extra help
 - body-parser
- middlewear which will be explained in more depth but think of it as a supportive layer of functionality for our app

 When we use the body parser middleware then the body sent from the client will be put onto req.body.  We don't have to do any of the heavy lifting from before in order to achieve this. 

Now we can tell our express app to **use** a particular bit of middle-ware, with the following line of code:

```js
const app = require('express')();
const bodyParser = require('body-parser');
app.use(bodyParser.json())

app.post('/cats', addCat);
```
If we sent a body in our POST request that looked like this:

```js

{"name" : "Shnitzel", "age" : "4"}

```

then we could now look at req.body where we will see this object.
