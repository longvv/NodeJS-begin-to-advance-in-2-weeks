# Error Handling and Logging in Node.js

## 1. Custom Error Classes

Custom error classes allow you to create specific error types for different scenarios in your application.

### Creating Custom Error Classes

```javascript
class ApplicationError extends Error {
  constructor(message, status) {
    super(message);
    this.name = this.constructor.name;
    this.status = status || 500;
    Error.captureStackTrace(this, this.constructor);
  }
}

class ResourceNotFoundError extends ApplicationError {
  constructor(resource = 'resource') {
    super(`The requested ${resource} was not found`, 404);
  }
}

class ValidationError extends ApplicationError {
  constructor(errors) {
    super('Validation error', 400);
    this.errors = errors;
  }
}
```

### Using Custom Error Classes

```javascript
app.get('/users/:id', (req, res, next) => {
  const user = findUser(req.params.id);
  if (!user) {
    return next(new ResourceNotFoundError('user'));
  }
  res.json(user);
});

app.post('/users', (req, res, next) => {
  const { username, email } = req.body;
  const errors = validateUser(username, email);
  if (errors.length > 0) {
    return next(new ValidationError(errors));
  }
  // Create user logic
});
```

## 2. Centralized Error Handling

Centralized error handling allows you to manage all errors in one place, ensuring consistent error responses across your application.

### Implementing Centralized Error Handling

```javascript
const express = require('express');
const app = express();

// ... Your routes and other middleware ...

// Centralized error handling middleware
app.use((err, req, res, next) => {
  console.error(err);

  if (err instanceof ApplicationError) {
    return res.status(err.status).json({
      error: {
        name: err.name,
        message: err.message,
        status: err.status,
        ...(err.errors && { details: err.errors })
      }
    });
  }

  // Handle unexpected errors
  res.status(500).json({
    error: {
      name: 'InternalServerError',
      message: 'An unexpected error occurred',
      status: 500
    }
  });
});
```

### Handling Async Errors

For async route handlers, use a wrapper to catch errors:

```javascript
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get('/users', asyncHandler(async (req, res) => {
  const users = await fetchUsers();
  res.json(users);
}));
```

## 3. Logging with Winston

Winston is a versatile logging library for Node.js that allows for multiple transports (output destinations) and log levels.

### Setting up Winston

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'user-service' },
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}
```

### Using Winston in Your Application

```javascript
// In your application code
logger.info('User logged in', { userId: user.id });
logger.error('Failed to connect to database', { error: err });

// In your error handling middleware
app.use((err, req, res, next) => {
  logger.error('Unhandled error', {
    error: {
      name: err.name,
      message: err.message,
      stack: err.stack
    },
    request: {
      method: req.method,
      url: req.url,
      body: req.body,
      headers: req.headers
    }
  });

  // ... rest of error handling logic ...
});
```

## Integrating Everything

Let's put it all together in an Express application:

```javascript
const express = require('express');
const winston = require('winston');

// Set up Winston logger
const logger = winston.createLogger({
  // ... (as defined above)
});

// Custom error classes
class ApplicationError extends Error {
  // ... (as defined above)
}

class ResourceNotFoundError extends ApplicationError {
  // ... (as defined above)
}

const app = express();

// Middleware
app.use(express.json());

// Routes
app.get('/users/:id', (req, res, next) => {
  const user = findUser(req.params.id);
  if (!user) {
    logger.warn(`User not found: ${req.params.id}`);
    return next(new ResourceNotFoundError('user'));
  }
  logger.info(`User retrieved: ${user.id}`);
  res.json(user);
});

// Async handler wrapper
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get('/posts', asyncHandler(async (req, res) => {
  const posts = await fetchPosts();
  logger.info(`Fetched ${posts.length} posts`);
  res.json(posts);
}));

// Centralized error handling
app.use((err, req, res, next) => {
  logger.error('Error occurred', {
    error: {
      name: err.name,
      message: err.message,
      stack: err.stack
    },
    request: {
      method: req.method,
      url: req.url
    }
  });

  if (err instanceof ApplicationError) {
    return res.status(err.status).json({
      error: {
        name: err.name,
        message: err.message,
        status: err.status,
        ...(err.errors && { details: err.errors })
      }
    });
  }

  res.status(500).json({
    error: {
      name: 'InternalServerError',
      message: 'An unexpected error occurred',
      status: 500
    }
  });
});

// Start server
app.listen(3000, () => {
  logger.info('Server started on port 3000');
});
```

This setup provides a robust error handling and logging system for your Node.js application. It allows for custom error types, centralized error handling, and detailed logging of both errors and application events.
