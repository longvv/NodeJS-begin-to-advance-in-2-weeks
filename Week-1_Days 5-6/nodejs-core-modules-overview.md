# Node.js Core Modules: fs, path, HTTP/HTTPS, events, util, stream

## 1. fs (File System)

The `fs` module provides an API for interacting with the file system.

Key functions:
- `readFile()`, `writeFile()`: Read/write files asynchronously
- `readFileSync()`, `writeFileSync()`: Read/write files synchronously
- `mkdir()`, `rmdir()`: Create/remove directories
- `readdir()`: Read directory contents
- `stat()`: Get file information

Example:
```javascript
const fs = require('fs').promises;

async function readAndWriteFile() {
  try {
    const data = await fs.readFile('input.txt', 'utf8');
    await fs.writeFile('output.txt', data.toUpperCase());
    console.log('File written successfully');
  } catch (err) {
    console.error('Error:', err);
  }
}

readAndWriteFile();
```

## 2. path

The `path` module provides utilities for working with file and directory paths.

Key functions:
- `join()`: Join path segments
- `resolve()`: Resolve to an absolute path
- `basename()`, `dirname()`, `extname()`: Get various parts of a path

Example:
```javascript
const path = require('path');

const fullPath = '/home/user/documents/file.txt';

console.log(path.basename(fullPath));  // file.txt
console.log(path.dirname(fullPath));   // /home/user/documents
console.log(path.extname(fullPath));   // .txt

const newPath = path.join('/home/user', 'documents', 'file.txt');
console.log(newPath);  // /home/user/documents/file.txt
```

## 3. HTTP/HTTPS

The `http` and `https` modules allow you to create HTTP/HTTPS servers and make HTTP/HTTPS requests.

Key functions:
- `http.createServer()`: Create an HTTP server
- `http.request()`, `https.request()`: Make HTTP/HTTPS requests

Example (HTTP server):
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

Example (HTTPS request):
```javascript
const https = require('https');

https.get('https://api.github.com/users/github', (res) => {
  let data = '';
  res.on('data', (chunk) => { data += chunk; });
  res.on('end', () => {
    console.log(JSON.parse(data));
  });
}).on('error', (err) => {
  console.error('Error: ', err.message);
});
```

## 4. events

The `events` module provides a way to work with events in Node.js.

Key class:
- `EventEmitter`: Allows objects to emit and listen for events

Example:
```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log('An event occurred!');
});

myEmitter.emit('event');
```

## 5. util

The `util` module provides utility functions useful for developers.

Key functions:
- `util.promisify()`: Convert callback-based functions to Promise-based
- `util.types`: Type checking functions
- `util.inspect()`: Convert object to string for debugging

Example:
```javascript
const util = require('util');
const fs = require('fs');

const readFileAsync = util.promisify(fs.readFile);

async function readFile() {
  try {
    const content = await readFileAsync('file.txt', 'utf8');
    console.log(content);
  } catch (err) {
    console.error('Error:', err);
  }
}

readFile();
```

## 6. stream

The `stream` module provides an abstract interface for working with streaming data.

Types of streams:
- Readable
- Writable
- Duplex
- Transform

Example (Reading from a stream):
```javascript
const fs = require('fs');

const readStream = fs.createReadStream('large-file.txt');

readStream.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes of data.`);
});

readStream.on('end', () => {
  console.log('Finished reading file.');
});
```

Example (Piping streams):
```javascript
const fs = require('fs');
const zlib = require('zlib');

fs.createReadStream('input.txt')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream('input.txt.gz'));

console.log('File compressed.');
```

These core modules provide essential functionality for file system operations, networking, event handling, utility functions, and working with streaming data in Node.js applications.
