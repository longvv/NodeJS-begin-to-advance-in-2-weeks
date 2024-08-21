# Express.js Middleware: Built-in, Custom, and Error-Handling

## Introduction to Middleware

Middleware functions in Express are functions that have access to the request object (req), the response object (res), and the next middleware function in the application's request-response cycle, commonly denoted by a variable named `next`.

## 1. Built-in Middleware

Express comes with several built-in middleware functions that you can use out of the box.

### express.json()

Parses incoming requests with JSON payloads.

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/api/data', (req, res) => {
  console.log(req.body);  // Contains parsed JSON
  res.json({ message: 'Data received' });
});
```

### express.urlencoded()

Parses incoming requests with URL-encoded payloads.

```javascript
app.use(express.urlencoded({ extended: true }));

app.post('/submit-form', (req, res) => {
  console.log(req.body);  // Contains parsed form data
  res.send('Form submitted');
});
```

### express.static()

Serves static files such as images, CSS files, and JavaScript files.

```javascript
app.use(express.static('public'));
```

This will serve static files from a directory named 'public' in your project root.

## 2. Custom Middleware

Custom middleware allows you to execute any code, make changes to the request and response objects, end the request-response cycle, or call the next middleware function.

### Basic Custom Middleware

```javascript
const myMiddleware = (req, res, next) => {
  console.log('This middleware was called at:', new Date());
  next();  // Call next() to pass control to the next middleware function
};

app.use(myMiddleware);
```

### Middleware with Parameters

```javascript
const authenticate = (role) => {
  return (req, res, next) => {
    if (req.user && req.user.role === role) {
      next();
    } else {
      res.status(403).send('Access denied');
    }
  };
};

app.get('/admin', authenticate('admin'), (req, res) => {
  res.send('Admin dashboard');
});
```

### Middleware for Specific Routes

```javascript
const logRequest = (req, res, next) => {
  console.log(`${req.method} request to ${req.url}`);
  next();
};

app.get('/api/users', logRequest, (req, res) => {
  res.json({ users: [] });
});
```

### Chaining Multiple Middleware

```javascript
const middleware1 = (req, res, next) => {
  console.log('Middleware 1');
  next();
};

const middleware2 = (req, res, next) => {
  console.log('Middleware 2');
  next();
};

app.get('/api/data', middleware1, middleware2, (req, res) => {
  res.send('Data');
});
```

## 3. Error-Handling Middleware

Error-handling middleware functions are defined with four arguments instead of three, specifically with the signature (err, req, res, next).

### Basic Error-Handling Middleware

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

### Custom Error-Handling Middleware

```javascript
class CustomError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
  }
}

const errorHandler = (err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  res.status(statusCode).json({
    status: 'error',
    statusCode: statusCode,
    message: err.message
  });
};

app.get('/api/item/:id', (req, res, next) => {
  const item = getItem(req.params.id);
  if (!item) {
    next(new CustomError(404, 'Item not found'));
  } else {
    res.json(item);
  }
});

app.use(errorHandler);
```

### Async Error Handling

When using async functions, make sure to catch errors and pass them to `next()`:

```javascript
app.get('/api/users', async (req, res, next) => {
  try {
    const users = await User.findAll();
    res.json(users);
  } catch (error) {
    next(error);
  }
});
```

## Best Practices for Middleware

1. **Keep middleware functions focused**: Each middleware should have a single responsibility.

2. **Order matters**: The order in which middleware is added with `app.use()` is important. First matched, first executed.

3. **Don't forget `next()`**: Always call `next()` unless you want to end the request-response cycle.

4. **Be careful with async middleware**: Use a try-catch block or a wrapper to handle errors in async middleware.

5. **Use middleware for cross-cutting concerns**: Things like logging, error handling, and authentication are good candidates for middleware.

6. **Organize middleware**: For larger applications, consider organizing middleware into separate modules.

## Conclusion

Middleware is a powerful feature in Express that allows you to add functionality to your application in a modular and reusable way. By understanding and effectively using built-in, custom, and error-handling middleware, you can create more robust, maintainable, and feature-rich Express applications.

