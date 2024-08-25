# Implementing CRUD Operations in Express.js

For this guide, let's assume we're building an API for a blog application with a 'Post' resource.

## Setup

First, let's set up our Express application and a simple in-memory data store:

```javascript
const express = require('express');
const app = express();
app.use(express.json());

let posts = [
  { id: 1, title: 'First Post', content: 'This is the first post.' },
  { id: 2, title: 'Second Post', content: 'This is the second post.' }
];

// Helper function to find a post by ID
function findPost(id) {
  return posts.find(post => post.id === parseInt(id));
}
```

## 1. Creating Resources (POST)

Use the POST method to create new resources.

```javascript
app.post('/api/posts', (req, res) => {
  // Input validation
  if (!req.body.title || !req.body.content) {
    return res.status(400).json({ error: 'Title and content are required' });
  }

  // Create new post
  const newPost = {
    id: posts.length + 1,
    title: req.body.title,
    content: req.body.content
  };

  // Add to our "database"
  posts.push(newPost);

  // Send response
  res.status(201).json(newPost);
});
```

Best Practices:
- Validate input data
- Return the created resource
- Use HTTP status 201 for successful creation

## 2. Reading Resources (GET)

Use GET to retrieve resources. Implement both collection (all posts) and individual resource retrieval.

```javascript
// Get all posts
app.get('/api/posts', (req, res) => {
  res.json(posts);
});

// Get a specific post
app.get('/api/posts/:id', (req, res) => {
  const post = findPost(req.params.id);
  
  if (!post) {
    return res.status(404).json({ error: 'Post not found' });
  }

  res.json(post);
});
```

Best Practices:
- Handle cases where the resource doesn't exist
- Consider implementing pagination for large collections
- Use query parameters for filtering and sorting

## 3. Updating Resources (PUT/PATCH)

Use PUT for full updates and PATCH for partial updates.

```javascript
// Full update (PUT)
app.put('/api/posts/:id', (req, res) => {
  const post = findPost(req.params.id);
  
  if (!post) {
    return res.status(404).json({ error: 'Post not found' });
  }

  // Validate input
  if (!req.body.title || !req.body.content) {
    return res.status(400).json({ error: 'Title and content are required' });
  }

  // Update post
  post.title = req.body.title;
  post.content = req.body.content;

  res.json(post);
});

// Partial update (PATCH)
app.patch('/api/posts/:id', (req, res) => {
  const post = findPost(req.params.id);
  
  if (!post) {
    return res.status(404).json({ error: 'Post not found' });
  }

  // Update only provided fields
  if (req.body.title) post.title = req.body.title;
  if (req.body.content) post.content = req.body.content;

  res.json(post);
});
```

Best Practices:
- Validate input data
- Handle cases where the resource doesn't exist
- Return the updated resource
- Use PUT for full updates and PATCH for partial updates

## 4. Deleting Resources (DELETE)

Use DELETE to remove resources.

```javascript
app.delete('/api/posts/:id', (req, res) => {
  const postIndex = posts.findIndex(post => post.id === parseInt(req.params.id));
  
  if (postIndex === -1) {
    return res.status(404).json({ error: 'Post not found' });
  }

  // Remove the post
  const deletedPost = posts.splice(postIndex, 1)[0];

  // Send response
  res.json(deletedPost);
});
```

Best Practices:
- Handle cases where the resource doesn't exist
- Consider using HTTP status 204 (No Content) if you don't want to return the deleted resource
- Implement soft delete if you need to maintain data integrity or allow for undoing deletions

## Error Handling

Implement proper error handling for your CRUD operations:

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});
```

## Middleware for Common Operations

For operations that are common across multiple routes (like finding a post), you can create middleware:

```javascript
function getPost(req, res, next) {
  const post = findPost(req.params.id);
  
  if (!post) {
    return res.status(404).json({ error: 'Post not found' });
  }

  req.post = post;
  next();
}

// Usage
app.get('/api/posts/:id', getPost, (req, res) => {
  res.json(req.post);
});

app.put('/api/posts/:id', getPost, (req, res) => {
  // Update logic here
});
```

## Testing CRUD Operations

Here's a simple way to test your CRUD operations using curl:

```bash
# Create a post
curl -X POST -H "Content-Type: application/json" -d '{"title":"New Post","content":"This is a new post"}' http://localhost:3000/api/posts

# Read all posts
curl http://localhost:3000/api/posts

# Read a specific post
curl http://localhost:3000/api/posts/1

# Update a post
curl -X PUT -H "Content-Type: application/json" -d '{"title":"Updated Post","content":"This post has been updated"}' http://localhost:3000/api/posts/1

# Partially update a post
curl -X PATCH -H "Content-Type: application/json" -d '{"title":"Partially Updated Post"}' http://localhost:3000/api/posts/1

# Delete a post
curl -X DELETE http://localhost:3000/api/posts/1
```

By implementing these CRUD operations, you've created a basic RESTful API for managing blog posts. Remember to adapt and expand these examples based on your specific application needs, including proper data persistence, authentication, and more sophisticated error handling.
