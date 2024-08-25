# ORM/ODM in Node.js: Mongoose and Sequelize

## Introduction

ORM (Object-Relational Mapping) and ODM (Object-Document Mapping) are techniques that let you query and manipulate data from a database using an object-oriented paradigm. 

- Mongoose is an ODM for MongoDB and Node.js
- Sequelize is an ORM for SQL databases (MySQL, PostgreSQL, SQLite, and Microsoft SQL Server)

## Mongoose (ODM for MongoDB)

### Setup

```javascript
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/myapp', {useNewUrlParser: true, useUnifiedTopology: true});
```

### Defining a Schema

```javascript
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, required: true, unique: true },
  age: { type: Number, min: 18 },
  createdAt: { type: Date, default: Date.now }
});

const User = mongoose.model('User', userSchema);
```

### CRUD Operations

1. Create

```javascript
const newUser = new User({ name: 'John Doe', email: 'john@example.com', age: 30 });
await newUser.save();
```

2. Read

```javascript
const users = await User.find({ age: { $gte: 18 } });
const john = await User.findOne({ email: 'john@example.com' });
```

3. Update

```javascript
await User.updateOne({ email: 'john@example.com' }, { age: 31 });
```

4. Delete

```javascript
await User.deleteOne({ email: 'john@example.com' });
```

### Validation

Mongoose provides built-in and custom validators:

```javascript
const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    validate: {
      validator: function(v) {
        return /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(v);
      },
      message: props => `${props.value} is not a valid email!`
    }
  }
});
```

### Middleware (Hooks)

Mongoose allows you to define pre and post hooks for various operations:

```javascript
userSchema.pre('save', function(next) {
  if (this.isModified('password')) {
    this.password = hashPassword(this.password);
  }
  next();
});
```

## Sequelize (ORM for SQL Databases)

### Setup

```javascript
const { Sequelize } = require('sequelize');
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql' // or 'postgres', 'sqlite', 'mssql'
});
```

### Defining a Model

```javascript
const User = sequelize.define('User', {
  name: Sequelize.STRING,
  email: {
    type: Sequelize.STRING,
    allowNull: false,
    unique: true
  },
  age: {
    type: Sequelize.INTEGER,
    validate: { min: 18 }
  },
  createdAt: Sequelize.DATE
});

await User.sync(); // This creates the table if it doesn't exist
```

### CRUD Operations

1. Create

```javascript
const newUser = await User.create({ name: 'John Doe', email: 'john@example.com', age: 30 });
```

2. Read

```javascript
const users = await User.findAll({ where: { age: { [Sequelize.Op.gte]: 18 } } });
const john = await User.findOne({ where: { email: 'john@example.com' } });
```

3. Update

```javascript
await User.update({ age: 31 }, { where: { email: 'john@example.com' } });
```

4. Delete

```javascript
await User.destroy({ where: { email: 'john@example.com' } });
```

### Validation

Sequelize provides built-in validators and allows custom validators:

```javascript
const User = sequelize.define('User', {
  email: {
    type: Sequelize.STRING,
    validate: {
      isEmail: true,
      customValidator(value) {
        if (value === 'test@test.com') {
          throw new Error('Please use a different email.');
        }
      }
    }
  }
});
```

### Hooks

Sequelize also supports hooks (called "lifecycle hooks"):

```javascript
User.beforeCreate(async (user, options) => {
  user.password = await hashPassword(user.password);
});
```

## Comparison

| Feature | Mongoose | Sequelize |
|---------|----------|-----------|
| Database | MongoDB | MySQL, PostgreSQL, SQLite, MSSQL |
| Schema | Enforced | Optional |
| Validation | Built-in | Built-in |
| Middleware | Yes (pre/post hooks) | Yes (lifecycle hooks) |
| Relationships | Yes | Yes |
| Transactions | Yes | Yes |
| Query Interface | MongoDB-like | SQL-like |
| TypeScript Support | Yes | Yes |

## Best Practices

1. **Use Migrations**: For Sequelize, use migrations to manage database schema changes over time.

2. **Indexing**: Create indexes for frequently queried fields in both Mongoose and Sequelize.

3. **Validation**: Use schema/model-level validations to ensure data integrity.

4. **Error Handling**: Implement proper error handling for database operations.

5. **Connection Pooling**: Use connection pools in Sequelize for better performance.

6. **Eager Loading**: Use populate in Mongoose and include in Sequelize to efficiently load related data.

7. **Transactions**: Use transactions for operations that need to be atomic.

Both Mongoose and Sequelize are powerful tools that simplify database operations in Node.js. The choice between them typically depends on whether you're using a NoSQL (MongoDB) or SQL database for your project.
