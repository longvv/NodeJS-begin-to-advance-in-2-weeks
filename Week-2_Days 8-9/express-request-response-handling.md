# Express.js: Request/Response Handling

## 1. Parsing Request Bodies

Express.js provides middleware to parse various types of request bodies. 

### JSON Bodies

For parsing JSON payloads:

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/api/users', (req, res) => {
  console.log(req.body); // Contains parsed JSON
  res.json({ message: 'User created', user: req.body });
});
```

### URL-encoded Bodies

For parsing URL-encoded data (typically from HTML forms):

```javascript
app.use(express.urlencoded({ extended: true }));

app.post('/api/login', (req, res) => {
  const { username, password } = req.body;
  // Process login
});
```

### Raw Bodies

For parsing raw request bodies:

```javascript
app.use(express.raw());

app.post('/api/webhook', (req, res) => {
  console.log(req.body); // Buffer containing raw body
  res.sendStatus(200);
});
```

### Multipart/Form-data

For handling file uploads and form-data, you can use the `multer` middleware:

```javascript
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/api/upload', upload.single('file'), (req, res) => {
  console.log(req.file); // Contains uploaded file info
  console.log(req.body); // Contains text fields, if any
  res.json({ message: 'File uploaded successfully' });
});
```

## 2. Setting Response Headers

You can set response headers using the `res.set()` method or directly using `res.header()`.

### Setting a Single Header

```javascript
app.get('/api/data', (req, res) => {
  res.set('Content-Type', 'application/json');
  // or
  res.header('Content-Type', 'application/json');
  
  res.json({ data: 'some data' });
});
```

### Setting Multiple Headers

```javascript
app.get('/api/secure-data', (req, res) => {
  res.set({
    'Content-Type': 'application/json',
    'Cache-Control': 'no-store',
    'Pragma': 'no-cache'
  });
  
  res.json({ secureData: 'sensitive information' });
});
```

### Common Headers to Consider

1. **CORS Headers**:
```javascript
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
  next();
});
```

2. **Security Headers**:
```javascript
app.use((req, res, next) => {
  res.set({
    'Strict-Transport-Security': 'max-age=31536000; includeSubDomains',
    'X-XSS-Protection': '1; mode=block',
    'X-Frame-Options': 'DENY',
    'X-Content-Type-Options': 'nosniff'
  });
  next();
});
```

## 3. Status Codes

Using appropriate status codes is crucial for clear communication between your API and its clients.

### Common Status Codes

1. **200 OK**: The request was successful
```javascript
app.get('/api/users', (req, res) => {
  res.status(200).json({ users: [] });
});
```

2. **201 Created**: The request was successful and a resource was created
```javascript
app.post('/api/users', (req, res) => {
  // Create user logic
  res.status(201).json({ message: 'User created', userId: newUser.id });
});
```

3. **204 No Content**: The request was successful, but there's no content to send back
```javascript
app.delete('/api/users/:id', (req, res) => {
  // Delete user logic
  res.sendStatus(204);
});
```

4. **400 Bad Request**: The request was invalid or cannot be served
```javascript
app.post('/api/users', (req, res) => {
  if (!req.body.username) {
    return res.status(400).json({ error: 'Username is required' });
  }
  // Create user logic
});
```

5. **401 Unauthorized**: The request requires authentication
```javascript
app.get('/api/protected', (req, res) => {
  if (!req.headers.authorization) {
    return res.status(401).json({ error: 'No authorization token provided' });
  }
  // Protected route logic
});
```

6. **403 Forbidden**: The server understood the request but refuses to authorize it
```javascript
app.delete('/api/users/:id', (req, res) => {
  if (req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Only admins can delete users' });
  }
  // Delete user logic
});
```

7. **404 Not Found**: The requested resource could not be found
```javascript
app.get('/api/users/:id', (req, res) => {
  const user = findUser(req.params.id);
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  res.json(user);
});
```

8. **500 Internal Server Error**: The server encountered an unexpected condition
```javascript
app.get('/api/data', (req, res) => {
  try {
    // Some operation that might throw an error
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: 'An unexpected error occurred' });
  }
});
```

### Best Practices for Status Codes

1. Be consistent in your use of status codes across your API.
2. Use the most specific status code that applies to the situation.
3. Include helpful error messages in the response body for non-2xx status codes.
4. Consider using custom error classes to standardize error responses:

```javascript
class AppError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}

app.use((err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';

  res.status(err.statusCode).json({
    status: err.status,
    message: err.message
  });
});
```

By mastering these aspects of request/response handling, you'll be able to create more robust, clear, and standards-compliant Express.js applications.
