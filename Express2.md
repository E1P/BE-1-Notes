## Topics for today

## Recap

* Recap setting up an express application
* What dependencies do we need for an express server
  .e.g body-parser etc
* What do controller functions always take : `req` and `res` (at the moment)
* How do we send information back to the client side?
* How do we get additional information from the url? Go over `req.params` and `req.query`

## Learning Objectives

Students should be able to:

* Understand what a **middleware** function is
* Refactor to mount routes.
* Understand Next and Error Handling


## Solution:  fetchByOwnerId
  - Introduce concept of reusable error handling by using the finalCb on every error rather than console.logging

  - app.js setup, with 
  ```js
  app.get( '/owner/:id/pets', getOwnersPets)
  ```
  - listen.js
  - setup of controller in controller folder
  - setup of model in models folder which makes calls to fs and data

## Express Middleware

In express, a middleware function is a function that has access to the request object, the response object and the next middleware function in the request-response cycle.
An express application is basically a chain of middleware middleware function calls.
 
Generally middleware functions provide an extra layer of functionality to our server and essentially can be thought of as a list of functions that a request must flow through before hitting the application logic: it's also therefore important to think about the placement of our middleware functions 

So far we've seen middleware in the form of body-parser which is a function call before we get to our routing and controller logic to deal with any possible incoming req.bodys. In this lecture, we will add router and error-handling middleware to our series of middleware calls in the request-response cycle.


## Refactoring to mount routes

If we have all of the routes and controllers in a single file, it starts to get in a bit of a mess and as the application grows, it will become an even bigger mess.

We all know that splitting code up into it's own single purpose module is useful and express gives us an easy way to facilitate this in regards to splitting up server routes into smaller subrouters.

This is where we are currently at:

```js
const app = require('express')();

app.get('/api/users', (req, res) => {
  res.status(200).send('All OK from /api/users');
});

app.get('/api/users/:id', (req, res) => {
  res.status(200).send('All OK from /api/users/:id');
});

app.get('/api/users/:id/shoppingList', (req, res) => {
  res.status(200).send('All OK from /api/users/:id/shoppingList');
});
```
Let's create a `routes` directory and add a `api-router.js` file.
In here we need to do the following (copy parts over at a time)


Now the `app.js` needs to be refactored to mount routers

Now the file that is in charge of starting the server has only the minimal code.

We can create even more subrouters too, so within the apiRouter we can `use` another user. The best way to do this is to create another file for the route, so let's create a `user.js` file in the `routes` dir with the folliwing:

# Error Handling

- So far we've been handling our errors using `console.log` or `throw` - but this isn't ideal because what does the browser/ Postman do if there's an error? It hangs...
- So we know we want to send up some kind of message to the user if anything goes wrong, its only polite what would we send up if there is an error?
```js
if (err) res.status(500).send({err})
```
- what principle of coding are we breaking here if we do this in every controller ? DRY we don't want to keep repeating this line
- So ideally we'd like to be able to write a function that can do this for any controller. Where could we write that function? In app...
- We're adding an extra piece of functionality and using this middleware chain so what should the function take? `req,res,next` => if we're dealing with errors what other thing will we need? `err, req,res,next`

- What seperates an express error handling function from a normal middleware function is that it will have 4 paramaters, error first. `err, req, res, next`.

### So how can we send errors to this function? `next(err)` from our controller...