# Demonstrating Express.js 101

## **Live Code**

### **Step 1** - Setting up

Setup directory and files inside the cohort's repository, and install Express:
- `cd 01-junior-phase/06-node-express`
- `mkdir demo-server`
- `cd demo-server`
  - Demonstrate to students that there's nothing in the directory: `ls`.
- `npm init -y`
- // the flag '-y' stands for "yes" - this will say yes to all defaults so don't need to choose
  - Demonstrate to students that a `package.json` gets created: `ls`.
- `npm install express`
  - Demonstrate to students that a `package-lock.json` file and `node_modules` directory were created: `ls`.
- `touch server.js`

Type `nodemon server.js` so that we don't have to keep restarting our server after every change.
- In case running `nodemon` doesn't work, add this script (line 3) to the `package.json` file:
```json
...
  "scripts": {
    "start": "node_modules/.bin/nodemon server.js"
  }, ...
```

### **Step 2** - Creating server using Express

Let's start spinning up our Express server:
```js
const express = require('express')

// The express() function is a top-level function exported by the express module.
const app = express()

// Binds and listens for connections on the specified host and port. This method is identical to Node’s http.Server.listen().
// Similar to a gate being open in an airport terminal for a plane to pull into.
app.listen(3000, () => {
  console.log('Listening on http://localhost:3000/')
})
```
- **Demonstrate using Postman:**
  1. Make sure the server is not running, and send a request: `GET http://localhost:3000/`
      > Why this might have happened:
      > - The server couldn't send a response: Ensure that the backend is working properly
  2. Spin up the server, and send the same request:
      - Response: `404 Not Found`, which indicates that the browser was ***able*** to communicate with a given server, _but_ the server could ***not*** find what was requested.

### **Step 3** - Create an endpoint (`GET /`)

***Routing*** refers to determining how an application responds to a client request to a particular endpoint, which is a URI (or path) and a specific HTTP request method (GET, POST, and so on).
Each route can have one or more handler functions, which are executed when the route is matched ([documentation](https://expressjs.com/en/starter/basic-routing.html)).

- Route definition takes the following structure:
  - `app.METHOD(PATH, HANDLER)`
  - Where:
    - `app` is an instance of express.
    - `METHOD` is an HTTP request method, in lowercase.
    - `PATH` is a path on the server.
    - `HANDLER` is the function executed when the route is matched.

```js
// GET /
app.get('/', (req, res, next) => {
  console.log(`Where do you think I'll log?`)
  res.send('<h1>Hello, world!</h1>')
})
```
- **Prompt students:**
  - "Where do you think that console log will print?"
- **Demonstrate using Postman:**
  1. Send a request: `GET http://localhost:3000/`
  2. Show the response body:
      - Toggle from "Pretty" to "Raw" to "Preview"
  3. Show the headers:
      - `Content-Type` indicates the resource media's type. Per the Express documentation, when the parameter is a String, the method sets the `Content-Type` to `text/html`.
      - **Note:** We can override this by updating the headers manually before we send our response ([documentation](https://expressjs.com/en/4x/api.html#res.set)).
        - `res.set('Content-Type', 'text/plain')`

Update the response to include more HTML:
```js
  res.send(`
    <html lang="en">
    <head>
      <meta charset="UTF-8">>
      <title>Express Demo</title>
    </head>
    <body>
      <h1>Express: Hop on the train</h1>
      <h2>chooo chooo</h2>
    </body>
    </html>
  `)
```

### **Step 4** - Create another endpoint (`GET /data`)

Using Postman, demonstrate what our endpoints will typically send _eventually_ by sending a request Porter & Sail's endpoint, `GET porterandsail.com/api/v2/cities`.
  - If that doesn't work, try pokemon: https://pokeapi.co/api/v2/pokemon
  - Show that the `Content-Type` is set to `application/json`.

Now let's make another route that sends back some data about our users:
```js
// GET /users
app.get('/users', (req, res, next) => {
  // Show the request object (`IncomingMessage`)
  console.log('typeof req >>', typeof req)
  console.log('request >>', req)

  // Send back the collection of users
  res.send([{
      id: 1,
      firstName: 'Noelle',
      lastName: 'Laureano'
    }, {
      id: 2,
      firstName: 'Natalie',
      lastName: 'Lane',
    }, {
      id: 3,
      firstName: 'Jess',
      lastName: 'Bracht'
    }
  ])
})
```
- **Demonstrate using Postman:**
  1. Send a request: `GET http://localhost:3000/users`
  2. Show the response body:
      - Toggle from "Pretty" to "Raw" to "Preview"
  3. Show the headers:
      - `Content-Type` indicates the resource media's type. Per the Express documentation, when the parameter is an Array or Object, Express responds with the JSON representation.
