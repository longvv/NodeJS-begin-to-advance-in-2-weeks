# Deep Dive into Node.js Ecosystem Components

## Core Modules
<img width="1346" alt="Screenshot 2024-08-11 at 08 59 41" src="https://github.com/user-attachments/assets/1f2f316f-fa0f-42b9-a638-eacd65dc98db">


### 1. fs (File System)
- Purpose: Provides an API for interacting with the file system.
- Key features:
  - Read/write files
  - Create/delete directories
  - Watch for file changes
- Example:
  ```javascript
  const fs = require('fs').promises;
  
  async function readFile(path) {
    try {
      const data = await fs.readFile(path, 'utf8');
      console.log(data);
    } catch (error) {
      console.error('Error reading file:', error);
    }
  }
  ```

### 2. http/https
- Purpose: Create HTTP/HTTPS servers and make HTTP/HTTPS requests.
- Key features:
  - Create web servers
  - Make HTTP/HTTPS requests
  - Handle incoming requests and send responses
- Example:
  ```javascript
  const http = require('http');
  
  const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello World\n');
  });
  
  server.listen(8080, () => {
    console.log('Server running at http://localhost:8080/');
  });
  ```

### 3. path
- Purpose: Provides utilities for working with file and directory paths.
- Key features:
  - Join path segments
  - Normalize paths
  - Resolve relative paths
- Example:
  ```javascript
  const path = require('path');
  
  const fullPath = path.join('/home', 'user', 'documents', 'file.txt');
  console.log(fullPath); // /home/user/documents/file.txt
  ```

### 4. events
- Purpose: Provides a way to work with events.
- Key features:
  - Create custom events
  - Emit and listen for events
- Example:
  ```javascript
  const EventEmitter = require('events');
  
  class MyEmitter extends EventEmitter {}
  const myEmitter = new MyEmitter();
  
  myEmitter.on('event', () => {
    console.log('An event occurred!');
  });
  
  myEmitter.emit('event');
  ```

### 5. stream
- Purpose: Abstract interface for working with streaming data.
- Key features:
  - Read/write streams
  - Transform streams
  - Piping streams
- Example:
  ```javascript
  const fs = require('fs');
  
  const readStream = fs.createReadStream('input.txt');
  const writeStream = fs.createWriteStream('output.txt');
  
  readStream.pipe(writeStream);
  ```

### 6. util
- Purpose: Provides utility functions for developers.
- Key features:
  - Promisify callback-based functions
  - Type checking
  - Formatting and logging
- Example:
  ```javascript
  const util = require('util');
  const fs = require('fs');
  
  const readFile = util.promisify(fs.readFile);
  
  async function read() {
    const data = await readFile('file.txt', 'utf8');
    console.log(data);
  }
  ```

## Frameworks

### 1. Express.js
- Purpose: Fast, unopinionated, minimalist web framework for Node.js.
- Key features:
  - Robust routing
  - HTTP helpers (redirection, caching, etc.)
  - Middleware support
- Example:
  ```javascript
  const express = require('express');
  const app = express();
  
  app.get('/', (req, res) => {
    res.send('Hello World!');
  });
  
  app.listen(3000, () => {
    console.log('Server running on port 3000');
  });
  ```

### 2. Koa.js
- Purpose: Expressive HTTP middleware framework for Node.js.
- Key features:
  - Lightweight and expressive
  - Modern JavaScript features (async/await)
  - Customizable via middleware
- Example:
  ```javascript
  const Koa = require('koa');
  const app = new Koa();
  
  app.use(async ctx => {
    ctx.body = 'Hello World';
  });
  
  app.listen(3000);
  ```

### 3. Nest.js
- Purpose: Progressive TypeScript framework for building efficient and scalable server-side applications.
- Key features:
  - Decorators and TypeScript support
  - Dependency injection
  - Modular architecture
- Example:
  ```typescript
  import { Controller, Get } from '@nestjs/common';
  
  @Controller('cats')
  export class CatsController {
    @Get()
    findAll(): string {
      return 'This action returns all cats';
    }
  }
  ```

### 4. Hapi.js
- Purpose: Rich framework for building applications and services.
- Key features:
  - Built-in support for input validation
  - Caching
  - Authentication and authorization
- Example:
  ```javascript
  const Hapi = require('@hapi/hapi');
  
  const init = async () => {
    const server = Hapi.server({
      port: 3000,
      host: 'localhost'
    });
  
    server.route({
      method: 'GET',
      path: '/',
      handler: (request, h) => {
        return 'Hello World!';
      }
    });
  
    await server.start();
    console.log('Server running on %s', server.info.uri);
  };
  
  init();
  ```

## Libraries

### 1. Axios
- Purpose: Promise-based HTTP client for the browser and Node.js.
- Key features:
  - Make XMLHttpRequests from the browser
  - Make HTTP requests from Node.js
  - Support the Promise API
- Example:
  ```javascript
  const axios = require('axios');
  
  axios.get('https://api.example.com/data')
    .then(response => {
      console.log(response.data);
    })
    .catch(error => {
      console.error('Error:', error);
    });
  ```

### 2. Socket.io
- Purpose: Enables real-time, bidirectional and event-based communication.
- Key features:
  - Real-time bidirectional event-based communication
  - Works on every platform, browser or device
  - Auto-reconnection support
- Example:
  ```javascript
  const io = require('socket.io')(3000);
  
  io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('chat message', (msg) => {
      io.emit('chat message', msg);
    });
  });
  ```

### 3. Lodash
- Purpose: A modern JavaScript utility library delivering modularity, performance & extras.
- Key features:
  - Array and object manipulation
  - Function utilities (debounce, throttle, etc.)
  - String manipulation
- Example:
  ```javascript
  const _ = require('lodash');
  
  const users = [
    { 'user': 'fred',   'age': 48 },
    { 'user': 'barney', 'age': 36 },
    { 'user': 'fred',   'age': 40 }
  ];
  
  console.log(_.sortBy(users, ['user', 'age']));
  ```

### 4. Mongoose
- Purpose: Elegant MongoDB object modeling for Node.js.
- Key features:
  - Schema-based solution to model application data
  - Includes built-in type casting, validation, query building, business logic hooks
- Example:
  ```javascript
  const mongoose = require('mongoose');
  mongoose.connect('mongodb://localhost/test', {useNewUrlParser: true, useUnifiedTopology: true});
  
  const Cat = mongoose.model('Cat', { name: String });
  
  const kitty = new Cat({ name: 'Zildjian' });
  kitty.save().then(() => console.log('meow'));
  ```

### 5. Jest
- Purpose: Delightful JavaScript Testing Framework with a focus on simplicity.
- Key features:
  - Zero config for most JavaScript projects
  - Isolated tests
  - Great API for testing asynchronous code
- Example:
  ```javascript
  function sum(a, b) {
    return a + b;
  }
  
  test('adds 1 + 2 to equal 3', () => {
    expect(sum(1, 2)).toBe(3);
  });
  ```

This deep dive provides a more comprehensive understanding of each component in the Node.js ecosystem, including their purposes, key features, and basic usage examples.
