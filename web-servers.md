# Web Servers

## Recap

* Re-cap on the different types of HTTP requests :
  _GET_, _POST_, _PUT/PATCH_ and _DELETE_
* Go over the anatomy of a url : protocol, domain, path, queries
* Define a **parametric** endpoint
* Do an example of a get request using postman
* Ask students to identify some of the basic status codes.

## Learning Objectives

Students should

* Be able to create a server using the http module in node.
* Be able to identify and use the important properties of the request and response object.
* Understand how to assemble a body from packets of data sent over from the client side.
* Should be able to identify and understand the different roles of Model, Views and Controller in the MVC pattern
* What a controller is and what is its role in the MVC architecture.

## What is a web server?

* Any program that processes http requests and sends http responses accordingly.
* Listens for requests on a specified port

## What is a client?

* The application making the request



## Building a server from scratch

We can use the http library to build a server up from scratch.
Firstly, we need to create the actual server itself :

```js
const server = http.createServer();
```

The `createServer` method takes a single argument which is a request,response handler - a function that gets called everytime the server receives a request.

```js
const server = http.createServer((request, response) => {
  console.log(req);
});
```

At the moment though, our server is looking out for requests from the client side: we need to make ensure that our server is **listening** for requests.

```js
const server = http.createServer((request, response) => {
  console.log(req);
});

server.listen(9090);
```

The `listen` method takes as its argument the PORT number on which the server should be listening for client requests.

Now we can start to build up logic to send out a response

```js
const server = http.createServer((request, response) => {
  response.setHeader('Content-Type', 'application/json');
  response.statusCode = 200;
  res.write(JSON.stringify('THE HOME PAGE'));
  res.end();
});

server.listen(9090);
```

We need to use `res.end()` otherwise the browser will still be hanging: the request won't have ended!

> Q: Always sent up as JSON or XML format, Why?
- standardised and strict syntax
- allows you to PARSE it and continue on in your coding
prevents dangerous things e.g. functions that will delete your whole file system to get sent up
- So an important clarification to be made: when do i stringify and when do i parse? stringify when you are Sending to the client parse when you are recieving something from the client


# Making Endpoints 
## REST
- We're building a RESTFUL api: representational state transfer this doesn't mean much but essentially is an architectural way of designing apps

- correct use of HTTP CRUD method
- urls clearly representing resourcers in our server
- use of paramters
- stateless: data isnt held on server but accessed by it
