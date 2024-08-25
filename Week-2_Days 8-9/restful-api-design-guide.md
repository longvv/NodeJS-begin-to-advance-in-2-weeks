# Designing a RESTful API: URL Structure, Conventions, and Versioning

## URL Structure and Conventions

A well-designed URL structure is crucial for creating intuitive and easy-to-use APIs. Here are key principles and best practices:

### 1. Use Nouns, Not Verbs

RESTful APIs use HTTP methods to indicate actions, so your URLs should focus on resources (nouns) rather than actions (verbs).

Good:
```
GET /articles
POST /articles
GET /articles/123
```

Avoid:
```
GET /getArticles
POST /createArticle
GET /getArticle/123
```

### 2. Use Plural Nouns for Collections

Consistently use plural nouns for collection resources.

Good:
```
GET /articles
GET /users
```

Avoid mixing singular and plural:
```
GET /article
GET /users
```

### 3. Use Hierarchical Structure for Related Resources

Represent relationships in the URL structure.

```
GET /users/123/posts
GET /posts/456/comments
```

### 4. Use Query Parameters for Filtering, Sorting, and Pagination

Keep the base URL clean and use query parameters for operations on the data.

```
GET /articles?category=tech
GET /articles?sort=date&order=desc
GET /articles?page=2&limit=20
```

### 5. Use Hyphens for Multi-Word Resource Names

Hyphens are more readable in URLs than underscores or camelCase.

Good:
```
GET /blog-posts
```

Avoid:
```
GET /blog_posts
GET /blogPosts
```

### 6. Avoid File Extensions

Don't include file extensions in URLs. Use Accept headers for content negotiation.

Good:
```
GET /articles/123
```

Avoid:
```
GET /articles/123.json
```

### 7. Use Lowercase Letters in URLs

Consistently use lowercase letters in URLs for better readability and to avoid case-sensitivity issues.

```
GET /articles/recent
```

### 8. CRUD Function Names and Their Corresponding HTTP Methods

- Create → POST
- Read → GET
- Update → PUT/PATCH
- Delete → DELETE

## API Versioning

API versioning is crucial for maintaining backward compatibility while allowing your API to evolve. Here are common versioning strategies:

### 1. URL Versioning

Include the version number in the URL path.

```
GET /api/v1/articles
GET /api/v2/articles
```

Implementation in Express.js:

```javascript
const express = require('express');
const app = express();

const v1Router = express.Router();
const v2Router = express.Router();

v1Router.get('/articles', (req, res) => {
  // Handle v1 request
});

v2Router.get('/articles', (req, res) => {
  // Handle v2 request
});

app.use('/api/v1', v1Router);
app.use('/api/v2', v2Router);
```

Pros:
- Easy to implement
- Clear and visible

Cons:
- Can lead to URL proliferation
- Changing versions requires changing the URL

### 2. Header Versioning

Use a custom header to specify the API version.

```
GET /api/articles
Accept-version: v1
```

Implementation in Express.js:

```javascript
app.use('/api', (req, res, next) => {
  const version = req.headers['accept-version'];
  if (version === 'v1') {
    // Route to v1 handler
  } else if (version === 'v2') {
    // Route to v2 handler
  } else {
    // Handle invalid version
  }
});
```

Pros:
- Keeps URLs clean
- Allows for more fine-grained version control

Cons:
- Less visible
- Requires custom header handling

### 3. Accept Header Versioning

Use the Accept header with a custom media type.

```
GET /api/articles
Accept: application/vnd.myapp.v1+json
```

Implementation in Express.js:

```javascript
app.use('/api', (req, res, next) => {
  const acceptHeader = req.headers.accept;
  if (acceptHeader.includes('application/vnd.myapp.v1+json')) {
    // Route to v1 handler
  } else if (acceptHeader.includes('application/vnd.myapp.v2+json')) {
    // Route to v2 handler
  } else {
    // Handle invalid version
  }
});
```

Pros:
- Follows HTTP specifications
- Allows content negotiation

Cons:
- More complex to implement
- Less intuitive for API consumers

### 4. Query Parameter Versioning

Include the version as a query parameter.

```
GET /api/articles?version=1
```

Implementation in Express.js:

```javascript
app.use('/api', (req, res, next) => {
  const version = req.query.version;
  if (version === '1') {
    // Route to v1 handler
  } else if (version === '2') {
    // Route to v2 handler
  } else {
    // Handle invalid version
  }
});
```

Pros:
- Easy to implement
- Doesn't require URL changes

Cons:
- Can be easily overlooked
- Mixes versioning with other query parameters

## Best Practices for Versioning

1. **Start with V1**: Always start your API with version 1, even if it's the only version.

2. **Make Breaking Changes in New Versions**: Only create a new version when you're making breaking changes.

3. **Maintain Old Versions**: Continue to support older versions for a reasonable period.

4. **Document Version Differences**: Clearly document the differences between versions.

5. **Use Semantic Versioning**: Consider using semantic versioning (MAJOR.MINOR.PATCH) for your API versions.

6. **Provide Migration Guides**: When releasing a new version, provide guides on how to migrate from the previous version.

By following these URL structure conventions and implementing a thoughtful versioning strategy, you can create a RESTful API that is intuitive, maintainable, and evolves smoothly over time.
