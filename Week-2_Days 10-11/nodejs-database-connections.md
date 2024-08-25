# Node.js Database Connections: Connection Strings and Pooling

## Connection Strings

Connection strings are URLs or text strings that specify the details required to connect to a database. They typically include information such as the host, port, database name, username, and password.

### PostgreSQL Connection String

Format:
```
postgresql://[user[:password]@][host][:port][/database][?parameter1=value1&...]
```

Example:
```
postgresql://myuser:mypassword@localhost:5432/mydatabase
```

### MongoDB Connection String

Format:
```
mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[database][?options]]
```

Example:
```
mongodb://myuser:mypassword@localhost:27017/mydatabase
```

### MySQL Connection String

Format:
```
mysql://user:password@host:port/database
```

Example:
```
mysql://myuser:mypassword@localhost:3306/mydatabase
```

## Connecting to Databases

### PostgreSQL with node-postgres

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: 'postgresql://myuser:mypassword@localhost:5432/mydatabase'
});

async function queryDatabase() {
  const client = await pool.connect();
  try {
    const result = await client.query('SELECT * FROM users');
    console.log(result.rows);
  } finally {
    client.release();
  }
}

queryDatabase();
```

### MongoDB with Mongoose

```javascript
const mongoose = require('mongoose');

const uri = 'mongodb://myuser:mypassword@localhost:27017/mydatabase';

mongoose.connect(uri, { useNewUrlParser: true, useUnifiedTopology: true });

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function() {
  console.log('Connected to MongoDB');
});
```

### MySQL with mysql2

```javascript
const mysql = require('mysql2/promise');

async function connectToDatabase() {
  const connection = await mysql.createConnection({
    host: 'localhost',
    user: 'myuser',
    password: 'mypassword',
    database: 'mydatabase'
  });
  
  console.log('Connected to MySQL');
  return connection;
}

connectToDatabase();
```

## Connection Pooling

Connection pooling is a technique used to maintain a cache of database connections that can be reused when future requests to the database are required. This significantly reduces the overhead of creating a new connection for every database operation.

### Benefits of Connection Pooling

1. Improved performance by reusing existing connections
2. Reduced latency in servicing requests
3. Better management of database resources
4. Ability to handle a large number of concurrent database operations

### PostgreSQL Connection Pooling

node-postgres has built-in connection pooling:

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: 'postgresql://myuser:mypassword@localhost:5432/mydatabase',
  max: 20, // Maximum number of clients in the pool
  idleTimeoutMillis: 30000, // How long a client is allowed to remain idle before being closed
  connectionTimeoutMillis: 2000, // How long to wait when connecting a new client
});

async function queryDatabase() {
  const client = await pool.connect();
  try {
    const result = await client.query('SELECT * FROM users');
    console.log(result.rows);
  } finally {
    client.release();
  }
}
```

### MongoDB Connection Pooling

Mongoose manages connection pooling automatically. You can configure it like this:

```javascript
const mongoose = require('mongoose');

mongoose.connect('mongodb://myuser:mypassword@localhost:27017/mydatabase', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  poolSize: 10 // Maintain up to 10 socket connections
});
```

### MySQL Connection Pooling

mysql2 provides connection pooling:

```javascript
const mysql = require('mysql2');

const pool = mysql.createPool({
  host: 'localhost',
  user: 'myuser',
  password: 'mypassword',
  database: 'mydatabase',
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0
});

// Use the pool for queries
pool.query('SELECT * FROM users', (err, results) => {
  if (err) throw err;
  console.log(results);
});
```

## Best Practices

1. **Environment Variables**: Store connection strings in environment variables, not in your code.

   ```javascript
   const pool = new Pool({
     connectionString: process.env.DATABASE_URL
   });
   ```

2. **Connection Limits**: Set appropriate connection limits based on your application's needs and the database server's capacity.

3. **Error Handling**: Implement robust error handling for database connections and queries.

4. **Connection Timeouts**: Set reasonable connection timeouts to avoid hanging requests.

5. **SSL Connections**: Use SSL for database connections in production environments.

   ```javascript
   const pool = new Pool({
     connectionString: process.env.DATABASE_URL,
     ssl: {
       rejectUnauthorized: false // Use this only if you're using a self-signed certificate
     }
   });
   ```

6. **Monitoring**: Implement monitoring for your database connections to track usage and identify issues.

By understanding and properly implementing database connections and connection pooling, you can significantly improve the performance and reliability of your Node.js applications.
