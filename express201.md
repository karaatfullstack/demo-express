# Demonstrating Express.js 201

## **Live Code**

### **Step 1** - Adding URL parameters

URL parameters allow us to create dynamic endpoints.

Using Postman, demonstrate query parameters by sending a request Porter & Sail's endpoint, `GET porterandsail.com/api/v2/cities/1`.
  - Change the ending integer to see other city-specific data.
  - Append `/hotels` after the integer to see the city's hotels.
    - **Note:** There is no such thing as `/cities/:cityId/hotels/:hotelId` because at that point, `/hotels` becomes its own resource.

Now let's make another route that sends back some data specific to a user ([documentation](https://expressjs.com/en/4x/api.html#req.params)):
```js
// GET /users/:id
app.get('/users/:id', (req, res, next) => {
  // Show the req.params object
  console.log('req.params >>', req.params)

  // Access the `id` parameter
  const userId = req.params.id
  console.log('userId >>', userId)

  res.send(users[userId])
})

// Mock database:
const users = {
  1: {
    id: 1,
    firstName: 'Noelle',
    lastName: 'Laureano',
    luckyNumber: 4,
    favoriteMeal: 'breakfast',

  },
  2: {
    id: 2,
    firstName: 'Natalie',
    lastName: 'Lane',
    luckyNumber: 10,
    favoriteMeal: 'lunch'
  },
  3: {
    id: 3,
    firstName: 'Jess',
    lastName: 'Bracht',
    luckyNumber: 1,
    favoriteMeal: 'dinner'
  }
}
```

- **Demonstrate using Postman:**
  1. Send a request: `GET http://localhost:3000/users/1`
  2. Change the id and send another request: `GET http://localhost:3000/users/2`

### **Step 2** - Incorporating `app.use` and `next`

Let's incorporate a new `app` function into our application:
```js
app.use('*', (req, res, next) => {
  console.log(req.method, req.baseUrl)

  // To close the request-response cycle, the server *must* send a response else the client just hangs waiting for one until it errors out
  // If we want to send the client a response in a different handler, we invoke `next`, our third parameter so that the request can flow through to the next middleware function
  next()
})
```
- **Demonstrate using Postman:**
  1. Comment out `next` and send a request: `GET http://localhost:3000/`
      - Show the log in the terminal
      - In Postman, show how the client "hangs" waiting for the server to respond:
          > Could not get any response\
          > There was an error connecting to http://localhost:3000/users/1.
  2. Uncomment `next` and send the same request
      - Show the log in the terminal
      - Show the expected response from the `GET /` route

### **Step 3** - Express middleware

**[Guide: Using middleware](http://expressjs.com/en/guide/using-middleware.html)**

Express is a routing and middleware web framework that has minimal functionality of its own: An Express application is essentially a series of middleware function calls.

Middleware functions are functions that have access to the request object (`req`), the response object (`res`), and the next middleware function in the application’s request-response cycle. The next middleware function is commonly denoted by a variable named `next`.

`app.use([path,] callback [, callback...])`
- `app.use` mounts the specified middleware function or functions at the specified path: the middleware function is executed when the base of the requested path matches `path`.

With that being said, instead of making our own middleware that logs out the request method and path, let's install and leverage the npm package [`morgan`](https://www.npmjs.com/package/morgan).
```js
const morgan = require('morgan')

app.use(morgan('dev'))
// 'dev' - Concise output colored by response status for development use. The :status token will be colored green for success codes, red for server error codes, yellow for client error codes, cyan for redirection codes, and uncolored for information codes.
```
- **Demonstrate using Postman:**
  1. Send a request: `GET http://localhost:3000/`
  2. Show the `morgan` middleware logs in action in the terminal

### **Step 4** - Serving static files

First, let's demonstrate sending a file from the server to the client:
1. Create a `public` folder
    - Add an image (e.g. `jpg`) file
2. Create a new route (_update path and filename as necessary_):
    - [`res.sendFile(path [, options] [, fn])`](https://expressjs.com/en/4x/api.html#res.sendFile)
    ```js
    app.get('/realmadrid.jpg', (req, res, next) => {
      res.sendFile(path.join(__dirname, 'public', 'realmadrid.jpg'))
    })
    ```

The problem with this is that we usually have way more assets than just a single image. For example, stylesheets (CSS), JavaScript, etc.

3. Create an `index.html` file in the `public` directory
4. Extract the HTML from the `GET /` route and paste it into the newly created `index.html` file:

    ```html
    <html lang="en">
      <head>
        <meta charset="UTF-8">>
        <title>Express Demo</title>
      </head>
      <body>
        <h1>Express: Hop on the train</h1>
        <h2>chooo chooo</h2>
        <img src="realmadrid.jpg"/>
      </body>
    </html>
    ```

  5. To serve static files such as images, CSS files, and JavaScript files, use the `express.static` built-in middleware function. See [Getting started: Serving static files in Express](https://expressjs.com/en/starter/static-files.html).\
  [`express.static(root, [options])`](https://expressjs.com/en/4x/api.html#express.static)
      - The `root` argument specifies the root directory from which to serve static assets. The function determines the file to serve by combining `req.url` with the provided `root` directory. When a file is not found, instead of sending a 404 response, it instead calls `next()` to move on to the next middleware, allowing for stacking and fall-backs.
      - The path that you provide to the `express.static` function is relative to the directory from where you launch your `node` process. If you run the express app from another directory, it’s safer to use the absolute path of the directory that you want to serve.

      ```js
      app.use(express.static(path.join(__dirname, 'public')))
      ```

- **Demonstrate using Postman:**
  1. Send a request: `GET http://localhost:3000/`
  2. Show the response:
      - Toggle from "Pretty" to "Raw" to "Preview"
