Detective, here are your tasks for the next two days:

# Introduction On day 1 we'll fetch data from the server about the northcoders and store it locally. On day 2 we'll leak this data to the world and create our own web server that will respond with the data we collected.

# Day 1

## Core Tasks

1. Use node's HTTP module to retrieve a list of all the available people at the following end point: https://nc-leaks.herokuapp.com/api/people.

2. Once you have the list of people. Find all of the northcoders from the list and save their data to a file.

3. Now you have access to the northcoders' usernames, you can obtain more information for each person. Retrieve everyone's interests and store it in another file from https://nc-leaks.herokuapp.com/api/people/:username/interests.

4. Do the same for everyone's pets from https://nc-leaks.herokuapp.com/api/people/:username/pets.

5. Automation is great. Automate the above steps by putting them in a single npm command. Think about how you are going to store the data, and what file format will be best. Make sure you read the relevant documentation (hint: [node http docs](https://nodejs.org/api/http.html)).

## Advanced Task

1. Aquire the final super secret instructions to claim a reward from https://nc-leaks.herokuapp.com/api/superSecret. To access it you will need to include headers in your get request. The key is 'secretkey' with value 'fae53351b9effc708e764e871bef3119'.

2. Use a library such as [`superagent`](https://github.com/visionmedia/superagent) to make your requests easier to handle. Refactor your pets in interests requests to use superagent instead of node's http module. # Day 2 Today we will leak this information to the world! You will introduce yourself to web servers and RESTful API's by building a server that serves all the data you collected yesterday. ## Core Tasks 1. Using Node's http module create a web server that responds with 'hello' when it receives a GET request on the path /api 2. Add a GET /api/northcoders endpoint that serves a JSON object of all the northcoders which shows their name, job details, image and username. 3. Add a GET /api/northcoders/:username parametric endpoint which serves the above information for one northcoder.

3. Add a GET /api/pets/:username parametric endpoint which serves a northcoders pets.

4. Add a GET /api/interests/:username parametric endpoint which serves a northcoders interests.

5. Add some error handling to your server. What should it respond with if you recieve a request for a username that doesn't exist? What status code should you respond with?

## Advanced Tasks

1. Add a query paramter to your GET /api/northcoders endpoint that searches for northcoders with a specific job. e.g. all the block-leads or all the marketing team. How should the endpoint respond by default(i.e. when not provided the query parameter?) How should it behave when provided an invalid parameter? What status code should your server respond with?

2. Add a GET /api/pets endpoint which accepts a query that serves all northcoders who have a living or dead pet 3. Add a query parameter to your GET /api/pets endpoint that serves all northcoders who have a certain kind of pet (e.g. cat, alpaca, llama etc) ",
