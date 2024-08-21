# Setting up an Express Server: Structure and Request Handling

## 1. Basic Server Structure

Let's start by setting up a basic Express server.

### Step 1: Initialize your project
First, create a new directory for your project and initialize it with npm:

```bash
mkdir express-server
cd express-server
npm init -y
```

### Step 2: Install Express
Install Express as a dependency:

```bash
npm install express
```

### Step 3: Create your server file
Create a new file called `server.js` and add the following code:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

This code does the following:
1. Imports the Express module
2. Creates an Express application
3. Defines a route for the root path ('/')
4. Starts the server on port 3000

### Step 4: Run your server
Run your server using Node.js:

```bash
node server.js
```

You should see the message "Server running at http://localhost:3000" in your console.

## 2. Handling Requests and Responses

Express provides a rich set of features for handling various types of requests and sending responses.

### Handling Different HTTP Methods

Express allows you to handle different HTTP methods (GET, POST, PUT, DELETE, etc.) easily:

```javascript
// GET request
app.get('/users', (req, res) => {
  res.send('GET request to the /users');
});

// POST request
app.post('/users', (req, res) => {
  res.send('POST request to the /users');
});

// PUT request
app.put('/users/:id', (req, res) => {
  res.send(`PUT request to update user with ID ${req.params.id}`);
});

// DELETE request
app.delete('/users/:id', (req, res) => {
  res.send(`DELETE request to remove user with ID ${req.params.id}`);
});
```

### Handling Route Parameters

You can define routes with parameters:

```javascript
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params);
});
```

If you visit `/users/34/books/8989`, you'll see:
```json
{ "userId": "34", "bookId": "8989" }
```

### Handling Query Parameters

Query parameters can be accessed via `req.query`:

```javascript
app.get('/search', (req, res) => {
  const { q } = req.query;
  res.send(`Search results for: ${q}`);
});
```

If you visit `/search?q=express`, you'll see: "Search results for: express"

### Handling JSON Data

To handle JSON data in request bodies, use the built-in middleware:

```javascript
app.use(express.json());

app.post('/api/data', (req, res) => {
  console.log(req.body);  // Contains parsed JSON from the request body
  res.json({ message: 'Data received', data: req.body });
});
```

### Sending Different Types of Responses

Express provides various methods to send responses:

```javascript
// Sending plain text
app.get('/text', (req, res) => {
  res.send('This is a plain text response');
});

// Sending JSON
app.get('/json', (req, res) => {
  res.json({ message: 'This is a JSON response' });
});

// Sending a file
app.get('/file', (req, res) => {
  res.sendFile(__dirname + '/public/index.html');
});

// Redirecting
app.get('/redirect', (req, res) => {
  res.redirect('/new-page');
});

// Setting custom status code
app.get('/not-found', (req, res) => {
  res.status(404).send('Not Found');
});
```

### Error Handling

Express has a built-in error handler, but you can also create custom error handling middleware:

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

## Conclusion

This setup provides a solid foundation for an Express server. You can expand on this structure by adding more routes, implementing middleware for tasks like logging or authentication, and organizing your code into separate route files as your application grows.

Remember to handle errors appropriately and structure your code in a maintainable way as your application becomes more complex.
