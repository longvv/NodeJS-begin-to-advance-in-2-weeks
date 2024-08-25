# Express.js Best Practices

## 1. Project Structure

Organize your project for scalability and maintainability.

### Recommended Structure:

```
my-express-app/
│
├── config/                 # Configuration files
│   └── database.js
│
├── controllers/            # Route handlers
│   ├── userController.js
│   └── productController.js
│
├── middleware/             # Custom middleware
│   ├── auth.js
│   └── errorHandler.js
│
├── models/                 # Data models
│   ├── User.js
│   └── Product.js
│
├── routes/                 # Route definitions
│   ├── userRoutes.js
│   └── productRoutes.js
│
├── services/               # Business logic
│   ├── userService.js
│   └── productService.js
│
├── utils/                  # Utility functions
│   └── helpers.js
│
├── tests/                  # Test files
│
├── app.js                  # App entry point
└── package.json
```

### Example Usage:

```javascript
// app.js
const express = require('express');
const userRoutes = require('./routes/userRoutes');
const errorHandler = require('./middleware/errorHandler');

const app = express();

app.use('/api/users', userRoutes);
app.use(errorHandler);

module.exports = app;

// routes/userRoutes.js
const express = require('express');
const userController = require('../controllers/userController');

const router = express.Router();

router.get('/', userController.getAllUsers);

module.exports = router;
```

## 2. Error Handling

Implement consistent error handling across your application.

### Best Practices:

- Use a central error handling middleware
- Create custom error classes
- Log errors for debugging
- Send appropriate error responses to clients

### Example:

```javascript
// middleware/errorHandler.js
const errorHandler = (err, req, res, next) => {
  console.error(err.stack);

  res.status(err.statusCode || 500).json({
    status: 'error',
    statusCode: err.statusCode || 500,
    message: err.message || 'Internal Server Error',
  });
};

module.exports = errorHandler;

// utils/AppError.js
class AppError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;

// In your route handler
const getAllUsers = async (req, res, next) => {
  try {
    const users = await User.find();
    res.status(200).json({ users });
  } catch (error) {
    next(new AppError(500, 'Error fetching users'));
  }
};
```

## 3. Asynchronous Code

Use async/await for cleaner asynchronous code.

### Example:

```javascript
const getUser = async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      return next(new AppError(404, 'User not found'));
    }
    res.status(200).json({ user });
  } catch (error) {
    next(error);
  }
};
```

## 4. Environment Configuration

Use environment variables for configuration.

### Example:

```javascript
// config/database.js
module.exports = {
  url: process.env.DATABASE_URL,
  options: {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  },
};

// Usage
const dbConfig = require('./config/database');
mongoose.connect(dbConfig.url, dbConfig.options);
```

## 5. Security Best Practices

Implement security measures to protect your application.

### Best Practices:

- Use HTTPS
- Implement proper authentication and authorization
- Set security HTTP headers
- Validate and sanitize user input
- Implement rate limiting
- Keep dependencies updated

### Example:

```javascript
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');

const app = express();

// Set security HTTP headers
app.use(helmet());

// Implement rate limiting
const limiter = rateLimit({
  max: 100, // limit each IP to 100 requests per windowMs
  windowMs: 15 * 60 * 1000, // 15 minutes
  message: 'Too many requests from this IP, please try again later',
});
app.use('/api', limiter);

// Other middleware and routes...
```

## 6. Validation

Validate input data to ensure data integrity and security.

### Example using express-validator:

```javascript
const { body, validationResult } = require('express-validator');

app.post(
  '/user',
  [
    body('username').isLength({ min: 5 }),
    body('email').isEmail(),
    body('password').isLength({ min: 8 }),
  ],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

    // Process the valid data
  }
);
```

## 7. Database Operations

Use an ORM/ODM for database operations and implement data validation.

### Example using Mongoose:

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true, minlength: 8 },
});

const User = mongoose.model('User', userSchema);

module.exports = User;
```

## 8. Testing

Implement unit and integration tests for your application.

### Example using Jest and Supertest:

```javascript
const request = require('supertest');
const app = require('../app');

describe('GET /api/users', () => {
  it('should return all users', async () => {
    const res = await request(app).get('/api/users');
    expect(res.statusCode).toBe(200);
    expect(Array.isArray(res.body)).toBeTruthy();
  });
});
```

## 9. Logging

Implement proper logging for debugging and monitoring.

### Example using Winston:

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

// Usage
logger.info('This is an info message');
logger.error('This is an error message');
```

## 10. Performance Optimization

Optimize your application for better performance.

### Best Practices:

- Use gzip compression
- Implement caching strategies
- Optimize database queries
- Use a reverse proxy like Nginx
- Implement pagination for large data sets

### Example:

```javascript
const compression = require('compression');
app.use(compression());

// Implement pagination
app.get('/api/users', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const skip = (page - 1) * limit;

  const users = await User.find().skip(skip).limit(limit);
  res.json(users);
});
```

By following these best practices, you can create more robust, secure, and maintainable Express.js applications. Remember to always keep your application and its dependencies up to date, and continually review and refactor your code for improvements.
