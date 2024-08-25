# Testing Node.js Applications: Unit Testing, Integration Testing, and Mocking

## 1. Unit Testing with Jest

Jest is a popular testing framework for JavaScript applications, including Node.js.

### Setting up Jest

First, install Jest:

```bash
npm install --save-dev jest
```

Update your `package.json`:

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

### Writing Unit Tests

Let's say we have a simple function to test:

```javascript
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

Here's how we can write a test for it:

```javascript
// math.test.js
const { add } = require('./math');

describe('Math functions', () => {
  test('adds 1 + 2 to equal 3', () => {
    expect(add(1, 2)).toBe(3);
  });

  test('adds -1 + 1 to equal 0', () => {
    expect(add(-1, 1)).toBe(0);
  });
});
```

Run the tests with:

```bash
npm test
```

### Testing Asynchronous Code

Jest can handle asynchronous code. Let's test a function that returns a Promise:

```javascript
// async.js
function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => resolve('data'), 100);
  });
}

module.exports = { fetchData };
```

Test:

```javascript
// async.test.js
const { fetchData } = require('./async');

test('fetchData returns data', async () => {
  const data = await fetchData();
  expect(data).toBe('data');
});
```

## 2. Integration Testing

Integration tests check how different parts of your application work together. Let's test an Express.js route:

### Setting up Supertest

Install Supertest for HTTP assertions:

```bash
npm install --save-dev supertest
```

### Writing Integration Tests

Assuming we have an Express app:

```javascript
// app.js
const express = require('express');
const app = express();

app.get('/user', (req, res) => {
  res.json({ name: 'John Doe' });
});

module.exports = app;
```

We can test it like this:

```javascript
// app.test.js
const request = require('supertest');
const app = require('./app');

describe('GET /user', () => {
  it('responds with json containing a user object', async () => {
    const response = await request(app)
      .get('/user')
      .expect('Content-Type', /json/)
      .expect(200);
    
    expect(response.body).toEqual({ name: 'John Doe' });
  });
});
```

## 3. Mocking and Stubbing

Mocking and stubbing are techniques used to isolate the code being tested by replacing dependencies with controlled alternatives.

### Mocking with Jest

Jest has built-in mocking capabilities. Let's mock a database call:

```javascript
// user.js
const db = require('./db');

async function getUser(id) {
  return db.findUserById(id);
}

module.exports = { getUser };
```

Test with a mock:

```javascript
// user.test.js
jest.mock('./db');
const db = require('./db');
const { getUser } = require('./user');

test('getUser calls db.findUserById and returns the result', async () => {
  const mockUser = { id: 1, name: 'John' };
  db.findUserById.mockResolvedValue(mockUser);

  const user = await getUser(1);
  expect(user).toEqual(mockUser);
  expect(db.findUserById).toHaveBeenCalledWith(1);
});
```

### Stubbing External Services

For external services, we can use libraries like `nock` to stub HTTP requests:

```bash
npm install --save-dev nock
```

```javascript
// weather.js
const axios = require('axios');

async function getWeather(city) {
  const response = await axios.get(`http://api.weather.com/${city}`);
  return response.data;
}

module.exports = { getWeather };
```

Test with nock:

```javascript
// weather.test.js
const nock = require('nock');
const { getWeather } = require('./weather');

test('getWeather returns weather data for a city', async () => {
  const mockWeatherData = { temperature: 25, conditions: 'Sunny' };
  
  nock('http://api.weather.com')
    .get('/London')
    .reply(200, mockWeatherData);

  const weather = await getWeather('London');
  expect(weather).toEqual(mockWeatherData);
});
```

## Best Practices

1. **Test Structure**: Use `describe` for grouping related tests and `test` or `it` for individual test cases.

2. **Test Naming**: Use clear, descriptive names for your tests. Consider using the "Given-When-Then" format.

3. **Setup and Teardown**: Use `beforeEach`, `afterEach`, `beforeAll`, and `afterAll` for setup and cleanup.

4. **Avoid Testing Implementation Details**: Focus on testing the behavior and output of your functions, not their internal workings.

5. **Code Coverage**: Use Jest's built-in coverage reporting to identify untested parts of your code.

6. **Continuous Integration**: Integrate your tests into your CI/CD pipeline to run them automatically on every push.

7. **Mocking Best Practices**: Only mock what you need to, and prefer dependency injection to make your code more testable.

8. **Testing Environment**: Use `dotenv` to manage environment variables for different environments (development, test, production).

## Example: Putting It All Together

Let's create a simple user service with database interaction and test it:

```javascript
// userService.js
const db = require('./db');

class UserService {
  async createUser(userData) {
    if (!userData.name || !userData.email) {
      throw new Error('Name and email are required');
    }
    return db.insertUser(userData);
  }

  async getUserById(id) {
    const user = await db.findUserById(id);
    if (!user) {
      throw new Error('User not found');
    }
    return user;
  }
}

module.exports = UserService;
```

Now, let's write tests for this service:

```javascript
// userService.test.js
const UserService = require('./userService');
const db = require('./db');

jest.mock('./db');

describe('UserService', () => {
  let userService;

  beforeEach(() => {
    userService = new UserService();
    jest.clearAllMocks();
  });

  describe('createUser', () => {
    it('should create a user when valid data is provided', async () => {
      const userData = { name: 'John Doe', email: 'john@example.com' };
      const expectedUser = { id: 1, ...userData };
      db.insertUser.mockResolvedValue(expectedUser);

      const user = await userService.createUser(userData);
      expect(user).toEqual(expectedUser);
      expect(db.insertUser).toHaveBeenCalledWith(userData);
    });

    it('should throw an error when name is missing', async () => {
      const userData = { email: 'john@example.com' };
      await expect(userService.createUser(userData)).rejects.toThrow('Name and email are required');
    });
  });

  describe('getUserById', () => {
    it('should return a user when a valid id is provided', async () => {
      const expectedUser = { id: 1, name: 'John Doe', email: 'john@example.com' };
      db.findUserById.mockResolvedValue(expectedUser);

      const user = await userService.getUserById(1);
      expect(user).toEqual(expectedUser);
      expect(db.findUserById).toHaveBeenCalledWith(1);
    });

    it('should throw an error when user is not found', async () => {
      db.findUserById.mockResolvedValue(null);
      await expect(userService.getUserById(1)).rejects.toThrow('User not found');
    });
  });
});
```

This example demonstrates unit testing with mocking, error handling, and testing both successful and failure scenarios.

