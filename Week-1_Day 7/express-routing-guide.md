# Express.js Routing: Methods, Parameters, and Query Strings

## 1. Route Methods

Express supports all HTTP methods for defining routes. Here are the most common ones:

### GET

Used for retrieving resources:

```javascript
app.get('/users', (req, res) => {
  res.send('GET request to /users');
});
```

### POST

Used for creating new resources:

```javascript
app.post('/users', (req, res) => {
  res.send('POST request to /users');
});
```

### PUT

Used for updating existing resources:

```javascript
app.put('/users/:id', (req, res) => {
  res.send(`PUT request to /users/${req.params.id}`);
});
```

### DELETE

Used for deleting resources:

```javascript
app.delete('/users/:id', (req, res) => {
  res.send(`DELETE request to /users/${req.params.id}`);
});
```

### PATCH

Used for partial updates to resources:

```javascript
app.patch('/users/:id', (req, res) => {
  res.send(`PATCH request to /users/${req.params.id}`);
});
```

### ALL

Matches all HTTP methods for a path:

```javascript
app.all('/secret', (req, res) => {
  res.send('Accessing the secret section ...');
});
```

## 2. Route Parameters

Route parameters are named URL segments used to capture values at specific positions in the URL.

### Basic Usage

```javascript
app.get('/users/:userId', (req, res) => {
  res.send(`User ID: ${req.params.userId}`);
});
```

If you visit `/users/123`, you'll see: "User ID: 123"

### Multiple Parameters

You can define multiple parameters in a single route:

```javascript
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(`User ID: ${req.params.userId}, Book ID: ${req.params.bookId}`);
});
```

Visiting `/users/123/books/456` will show: "User ID: 123, Book ID: 456"

### Optional Parameters

You can make parameters optional by adding a question mark:

```javascript
app.get('/users/:userId?', (req, res) => {
  if (req.params.userId) {
    res.send(`User ID: ${req.params.userId}`);
  } else {
    res.send('User ID not provided');
  }
});
```

### Parameter with Regex

You can use regular expressions to constrain parameters:

```javascript
app.get('/users/:userId(\\d+)', (req, res) => {
  res.send(`User ID: ${req.params.userId}`);
});
```

This route will only match if `userId` consists of digits.

## 3. Query Strings

Query strings provide a way to pass additional data to the server in the URL.

### Accessing Query Strings

Query parameters are accessed via `req.query`:

```javascript
app.get('/search', (req, res) => {
  const { q, page = 1 } = req.query;
  res.send(`Search for: ${q}, Page: ${page}`);
});
```

Visiting `/search?q=express&page=2` will show: "Search for: express, Page: 2"

### Multiple Values for the Same Parameter

Query strings can have multiple values for the same key:

```javascript
app.get('/filter', (req, res) => {
  const { category } = req.query;
  if (Array.isArray(category)) {
    res.send(`Filtering by categories: ${category.join(', ')}`);
  } else {
    res.send(`Filtering by category: ${category}`);
  }
});
```

Visiting `/filter?category=books&category=electronics` will show: "Filtering by categories: books, electronics"

### Parsing Complex Query Strings

For complex query strings, you might want to use a library like `qs`:

```javascript
const qs = require('qs');

app.get('/complex', (req, res) => {
  const parsedQuery = qs.parse(req.query);
  res.json(parsedQuery);
});
```

## Advanced Routing Techniques

### Chaining Route Handlers

You can chain multiple handlers for a single route:

```javascript
app.route('/book')
  .get((req, res) => {
    res.send('Get a random book');
  })
  .post((req, res) => {
    res.send('Add a book');
  })
  .put((req, res) => {
    res.send('Update the book');
  });
```

### Using Express Router

For larger applications, use Express Router to modularize your routes:

```javascript
// In routes/users.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.send('Users home page');
});

router.get('/:id', (req, res) => {
  res.send(`User profile for ID ${req.params.id}`);
});

module.exports = router;

// In your main app file
const usersRouter = require('./routes/users');
app.use('/users', usersRouter);
```

### Error Handling in Routes

Implement error handling for your routes:

```javascript
app.get('/user/:id', async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      return res.status(404).send('User not found');
    }
    res.json(user);
  } catch (error) {
    next(error); // Pass errors to Express error handler
  }
});
```

## Best Practices

1. Keep your routes organized and modular using Express Router.
2. Use descriptive names for your routes and parameters.
3. Validate and sanitize user inputs, especially for route parameters and query strings.
4. Implement proper error handling for each route.
5. Use appropriate HTTP methods for different operations (GET for retrieval, POST for creation, etc.).
6. Consider versioning your API routes (e.g., `/api/v1/users`).

By mastering these routing techniques, you can create clean, efficient, and maintainable Express applications that handle a wide variety of request patterns.
