# Introducing pg-promise

## Learning Objectives

Students should:

- Understand how to use basic query methods from the [pg-promise](https://github.com/vitaly-t/pg-promise/wiki/Learn-by-Example) library.
- Have an intuition of how promises work to simplify asynchronous code.
- Understand how to use [supertest](https://www.npmjs.com/package/supertest) to test end-points

## Pg-Promise
> Pg-promise is a package we're going to use to interact and query our SQL database using javascript

- Install pg-promise

> We're going to create a db folder & db/connection.js file from where we're going to open a connection to our database
> pgp connection image

### This config object is going to allow us to configure what database we connect to using PGP (harry_potter)
> the port number refers to the port that pgp uses so you can't pick and choose this like your express ones... 5432 is where pgp lives

> on linux you'll also have to pass in a user password properties from when you set up postgres on your computers...

> because of private passwords we're going to put this object in another file, config.js and require it in - and we'll make sure to not push it up to github by putting it in the .gitignore file

> Db is now our connection to the database and any time we want to query the database we'll have to refer to this db

### Before we had our MVC structure where the models abstracted away calls to our json data using fs, then the data eventually resolved and got passed back to our controllers which we sent....
### But now we've got pgp which is doing most of the complexity for us so we can do it all in the controller

- First of all if we want to be able to query our database we'll have to bring in that connection from db.js
- Now to use pgp and query our database all we do is call db.method and pass it a SQL query string
- the many refers to how many rows I'm expecting back from the query, so there's also one, none manyornone

### Q: So... do you think querying a databse is synchronous?
### A: No, it's not...

> Q: How would we expect to get back the information from that query?

> A: Callbacks... which we'd pass as a second argument to db.many


## But note, this package is called pg-promise
### Promises are another way of dealing with asynchronisity and they solve a few issues for us
- callback hell where things become more nested
- and constantly having to handle errors

# Promises

- you'll be going much more in depth with them tomorrow... so for today you should just get comfortable using them
- so after sending off an asynchronous request like 
`db.many ` we can use a promise method in javascript `.then` and `.catch` 
- when we call `.then` we pass it a function, sort of like a callback, which will be invoked with res or the result of that asynchronous request 
- so the parameter to this function i'd label houses because thats what i'd like this request to resolve with
- once I'm in this function I can do my res.json

## What about if things go wrong? Errors

> Another benefit of promises is that we don't constantly have to check  `if (err)`
- To handle errors we have another method `.catch` which also takes a function
- If something goes wrong in any of this code in any of these `then`s, the error will be passed to the catch block

### If you've been throwing / logging your errors thus now we're going to handle them properly without breaking our server using `next` and an error handling function in our app file

# Testing

- If we were a company serving up a data api we need to make sure we're serving up the correct data on each endpoint

- For this we're going to use a package called `supertest` which is going to allow us to mock calls to our server and test the results we get back...

- this means we'll be making mock requests to our app
____

- here, nesting describe blocks makes sense because everything is under our umbrella api roue, and then we've got subrouters which expect different bits of data back

- when we get to our it desriptions we conventionally state the method we're testing on this describe's route, the status and data we're expecting back

- to actually call our api we're going to use this supertest request method, which we chain on the type of request we're sending and the endpoint we're trying to test

- to test the status code returned we can chain on a supertest expect method and pass it the expected status code

- supertest also uses promises, so how could we expect to wait for the data to come back? `then` which will be passed the response from our server

> where does our send information get written to? the response body

> so where and how should we construct our actual expect statement for this array of houses

> expect inside the `then` function

> how do we ensure the response is actually passed to the `then` function ? returning the request

# Testing POST requests

- same again return request to the endpoint
- chain on the `.post` method
- what status code would we expect? 201
- and what ideally would be a good thing to be returned... the thing posted itself

### But what needs to go on the request of a post ?  The information to post... on the req.body
### we'll use the `send` method and pass it an object that should hopefully go into our database so would make sense to give it properties that correspond to table names

## So lets write the controller for this...

> which db query would I use here?

> well i only expect my posted house back so one row... so db.one

> where do I get the info to post?  req.body => undefined? body-parser

### Now we have our info we want to save it, how would I phrase that SQL statement? and how would i put the variable values in there? string literals?

## However, in SQL this poses a massive risk **SQL INJECTION**
- someone could put on the body `house_name: 'house of weasley; DROP DATABASE`
- string literals would mean that colon would be read as an extra command and our database is dropped

- pgp allows us a method of inserting variables without this risk
- $ normally refers to place holders to we put a placeholder that matches a key on an object passed as an extra argument 

``` js
const {house_name, founder} = req.body
db.one('INSERT INTO HOUSES (house_name, founder) VALUES ($<house_name>, $<founder>)',req.body)
```

### Now, lastly, inserts by default don't return anything so to get the information back and pass our tests, we can add `'RETURNING *;'` to get the row we just inserted back

# test scripts

###  Now lets say I run this test... and i post... and it passes, what about the next time? it will fail... we'll be expecting id of 4, but now thats already run from the previous test so is 5...
- we need to start fresh otherwise all tests will start failing... and we want tests & data to be consistent...
- So we'll drop and re-seed our database before our tests

```"test": "psql -f seed.sql && mocha/spec"```