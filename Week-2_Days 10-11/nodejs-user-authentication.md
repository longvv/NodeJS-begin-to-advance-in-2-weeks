# User Authentication in Node.js: Password Hashing and Authentication Methods

## Password Hashing with bcrypt

Password hashing is crucial for protecting user credentials. bcrypt is a popular and secure hashing algorithm.

### Installing bcrypt

```bash
npm install bcrypt
```

### Hashing Passwords

```javascript
const bcrypt = require('bcrypt');
const saltRounds = 10; // The higher, the more secure, but slower

async function hashPassword(password) {
  try {
    const salt = await bcrypt.genSalt(saltRounds);
    const hash = await bcrypt.hash(password, salt);
    return hash;
  } catch (error) {
    console.error('Error hashing password:', error);
    throw error;
  }
}
```

### Verifying Passwords

```javascript
async function verifyPassword(password, hash) {
  try {
    const match = await bcrypt.compare(password, hash);
    return match;
  } catch (error) {
    console.error('Error verifying password:', error);
    throw error;
  }
}
```

### Usage Example

```javascript
async function registerUser(username, password) {
  const hashedPassword = await hashPassword(password);
  // Save username and hashedPassword to database
}

async function loginUser(username, password) {
  // Retrieve hashedPassword from database for this username
  const hashedPassword = // ... retrieved from database
  const isMatch = await verifyPassword(password, hashedPassword);
  if (isMatch) {
    // Passwords match, proceed with login
  } else {
    // Passwords don't match, reject login
  }
}
```

## Session-Based Authentication

Session-based authentication uses server-side sessions to keep track of logged-in users.

### Key Concepts

1. User logs in with credentials
2. Server creates a session and stores it server-side
3. Server sends a session ID to the client (usually as a cookie)
4. Client includes this session ID in subsequent requests
5. Server validates the session ID for each request

### Implementation with Express and express-session

```javascript
const express = require('express');
const session = require('express-session');
const bcrypt = require('bcrypt');

const app = express();

app.use(express.json());
app.use(session({
  secret: 'your-secret-key',
  resave: false,
  saveUninitialized: true,
  cookie: { secure: true } // Use secure cookies in production
}));

// Mock user database
const users = [];

app.post('/register', async (req, res) => {
  try {
    const { username, password } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    users.push({ username, password: hashedPassword });
    res.status(201).send('User registered');
  } catch (error) {
    res.status(500).send('Error registering user');
  }
});

app.post('/login', async (req, res) => {
  try {
    const { username, password } = req.body;
    const user = users.find(u => u.username === username);
    if (user && await bcrypt.compare(password, user.password)) {
      req.session.userId = username;
      res.send('Logged in');
    } else {
      res.status(401).send('Invalid credentials');
    }
  } catch (error) {
    res.status(500).send('Error logging in');
  }
});

app.get('/protected', (req, res) => {
  if (req.session.userId) {
    res.send('Access granted to protected resource');
  } else {
    res.status(401).send('Unauthorized');
  }
});

app.post('/logout', (req, res) => {
  req.session.destroy(err => {
    if (err) {
      res.status(500).send('Error logging out');
    } else {
      res.send('Logged out');
    }
  });
});
```

### Pros of Session-Based Auth

- Revoke sessions easily
- Can store additional session data
- Well-suited for web applications

### Cons of Session-Based Auth

- Server needs to store session data
- Harder to scale horizontally
- CSRF vulnerability if not protected against

## Token-Based Authentication (JWT)

Token-based authentication uses signed tokens (often JWTs) to authenticate requests.

### Key Concepts

1. User logs in with credentials
2. Server creates a signed token and sends it to the client
3. Client stores the token (e.g., in localStorage) and sends it with each request
4. Server validates the token for each request

### Implementation with Express and jsonwebtoken

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

const app = express();
app.use(express.json());

const SECRET_KEY = 'your-secret-key';
const users = [];

app.post('/register', async (req, res) => {
  try {
    const { username, password } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    users.push({ username, password: hashedPassword });
    res.status(201).send('User registered');
  } catch (error) {
    res.status(500).send('Error registering user');
  }
});

app.post('/login', async (req, res) => {
  try {
    const { username, password } = req.body;
    const user = users.find(u => u.username === username);
    if (user && await bcrypt.compare(password, user.password)) {
      const token = jwt.sign({ username }, SECRET_KEY, { expiresIn: '1h' });
      res.json({ token });
    } else {
      res.status(401).send('Invalid credentials');
    }
  } catch (error) {
    res.status(500).send('Error logging in');
  }
});

function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  if (token == null) return res.sendStatus(401);

  jwt.verify(token, SECRET_KEY, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
}

app.get('/protected', authenticateToken, (req, res) => {
  res.send(`Welcome, ${req.user.username}!`);
});
```

### Pros of Token-Based Auth

- Stateless, easier to scale
- Can be used across multiple domains
- Better suited for mobile applications and SPAs

### Cons of Token-Based Auth

- Tokens can't be easily revoked before they expire
- Need to secure token storage on client-side
- Larger request payload size

## Best Practices

1. Always use HTTPS to encrypt data in transit
2. Implement proper password policies (minimum length, complexity)
3. Use secure session settings (httpOnly, secure cookies)
4. Implement rate limiting to prevent brute-force attacks
5. Consider using refresh tokens with JWTs for long-lived sessions
6. Regularly update and patch all dependencies
7. Implement proper logging and monitoring for security events
8. Use security headers (HSTS, CSP, etc.)

Both session-based and token-based authentication have their use cases. Choose based on your application's specific requirements, architecture, and scalability needs.
