# Basic Database Operations in Node.js with MongoDB and Mongoose

## Setup

First, let's set up our MongoDB connection and define a simple schema:

```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// Define a schema
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true },
  age: Number,
  createdAt: { type: Date, default: Date.now }
});

// Create a model
const User = mongoose.model('User', userSchema);
```

## 1. Creating Documents/Records

To create a new document, we can use the `create` method or instantiate a new model and save it.

### Using `create` method:

```javascript
async function createUser(userData) {
  try {
    const newUser = await User.create(userData);
    console.log('User created:', newUser);
    return newUser;
  } catch (error) {
    console.error('Error creating user:', error);
    throw error;
  }
}

// Usage
createUser({ name: 'John Doe', email: 'john@example.com', age: 30 });
```

### Using model instantiation:

```javascript
async function createUserWithSave(userData) {
  try {
    const user = new User(userData);
    const savedUser = await user.save();
    console.log('User saved:', savedUser);
    return savedUser;
  } catch (error) {
    console.error('Error saving user:', error);
    throw error;
  }
}

// Usage
createUserWithSave({ name: 'Jane Doe', email: 'jane@example.com', age: 28 });
```

## 2. Querying Data

Mongoose provides various methods for querying data.

### Find all documents:

```javascript
async function getAllUsers() {
  try {
    const users = await User.find();
    console.log('All users:', users);
    return users;
  } catch (error) {
    console.error('Error fetching users:', error);
    throw error;
  }
}
```

### Find with conditions:

```javascript
async function getUsersByAge(minAge) {
  try {
    const users = await User.find({ age: { $gte: minAge } });
    console.log('Users found:', users);
    return users;
  } catch (error) {
    console.error('Error fetching users:', error);
    throw error;
  }
}

// Usage
getUsersByAge(25);
```

### Find one document:

```javascript
async function getUserByEmail(email) {
  try {
    const user = await User.findOne({ email: email });
    if (user) {
      console.log('User found:', user);
    } else {
      console.log('No user found with that email');
    }
    return user;
  } catch (error) {
    console.error('Error fetching user:', error);
    throw error;
  }
}

// Usage
getUserByEmail('john@example.com');
```

## 3. Updating Data

Mongoose provides several methods for updating documents.

### Update one document:

```javascript
async function updateUserAge(email, newAge) {
  try {
    const result = await User.updateOne({ email: email }, { age: newAge });
    console.log('Update result:', result);
    return result;
  } catch (error) {
    console.error('Error updating user:', error);
    throw error;
  }
}

// Usage
updateUserAge('john@example.com', 31);
```

### Find and update:

```javascript
async function findAndUpdateUser(email, updateData) {
  try {
    const updatedUser = await User.findOneAndUpdate(
      { email: email },
      updateData,
      { new: true } // This option returns the updated document
    );
    if (updatedUser) {
      console.log('Updated user:', updatedUser);
    } else {
      console.log('No user found with that email');
    }
    return updatedUser;
  } catch (error) {
    console.error('Error updating user:', error);
    throw error;
  }
}

// Usage
findAndUpdateUser('jane@example.com', { age: 29, name: 'Jane Smith' });
```

## 4. Deleting Data

Mongoose provides methods for deleting documents as well.

### Delete one document:

```javascript
async function deleteUserByEmail(email) {
  try {
    const result = await User.deleteOne({ email: email });
    console.log('Delete result:', result);
    return result;
  } catch (error) {
    console.error('Error deleting user:', error);
    throw error;
  }
}

// Usage
deleteUserByEmail('john@example.com');
```

### Find and delete:

```javascript
async function findAndDeleteUser(email) {
  try {
    const deletedUser = await User.findOneAndDelete({ email: email });
    if (deletedUser) {
      console.log('Deleted user:', deletedUser);
    } else {
      console.log('No user found with that email');
    }
    return deletedUser;
  } catch (error) {
    console.error('Error deleting user:', error);
    throw error;
  }
}

// Usage
findAndDeleteUser('jane@example.com');
```

## Best Practices

1. **Error Handling**: Always wrap database operations in try-catch blocks to handle potential errors.

2. **Validation**: Use Mongoose schema validation to ensure data integrity:

   ```javascript
   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true, lowercase: true },
     age: { type: Number, min: 18 }
   });
   ```

3. **Indexing**: Create indexes for frequently queried fields to improve performance:

   ```javascript
   userSchema.index({ email: 1 });
   ```

4. **Lean Queries**: Use `.lean()` for read-only operations to get plain JavaScript objects instead of Mongoose documents:

   ```javascript
   const users = await User.find().lean();
   ```

5. **Projection**: Specify which fields to include or exclude in your queries to reduce data transfer:

   ```javascript
   const user = await User.findOne({ email: 'john@example.com' }, 'name email');
   ```

By mastering these basic database operations, you'll be well-equipped to handle data in your Node.js applications efficiently and effectively.
