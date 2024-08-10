# How JavaScript Functions Work

## 1. Function Creation

When a function is created in JavaScript, several things happen:

### Function Object Creation
- JavaScript creates a function object.
- This object contains the function's code, as well as properties like `name`, `length` (number of parameters), and `prototype`.

### Lexical Environment Capture
- The function captures its lexical environment, which includes variables in its outer scope.

Example:
```javascript
function greet(name) {
    return `Hello, ${name}!`;
}
```

## 2. Function Execution

When a function is called, JavaScript creates a new execution context. This involves:

### Creation Phase
1. Create the Arguments object (not in arrow functions)
2. Create the `this` binding
3. Create the variable environment
   - Allocate memory for variables and function declarations
   - Initialize variables to `undefined` (for `var`)

### Execution Phase
1. Execute the function's code line by line
2. Assign values to variables as they appear in the code

Example:
```javascript
function add(a, b) {
    var result = a + b;
    return result;
}

add(3, 5);
```

## 3. The Call Stack

JavaScript uses a call stack to manage function execution:

1. When a function is called, a new frame is pushed onto the stack.
2. When a function returns, its frame is popped off the stack.

Example:
```javascript
function first() {
    console.log('First');
    second();
}

function second() {
    console.log('Second');
}

first();
```

Call Stack:
```
[second]
[first]
[global execution context]
```

## 4. Closures

Closures are a powerful feature of JavaScript functions:

- A closure is created when a function is defined within another function.
- It allows the inner function to access variables from the outer function's scope.

Example:
```javascript
function outer(x) {
    return function inner(y) {
        return x + y;
    };
}

const addFive = outer(5);
console.log(addFive(3)); // 8
```

## 5. `this` Binding

The value of `this` inside a function depends on how the function is called:

- In a method, `this` refers to the object the method was called on.
- In a regular function, `this` refers to the global object (or `undefined` in strict mode).
- In an arrow function, `this` is lexically bound (it keeps the `this` value of the enclosing scope).

Example:
```javascript
const obj = {
    name: 'Object',
    regularMethod: function() {
        console.log(this.name);
    },
    arrowMethod: () => {
        console.log(this.name);
    }
};

obj.regularMethod(); // 'Object'
obj.arrowMethod();   // undefined (or global object's name)
```

## 6. Function Parameters

- JavaScript functions can be called with any number of arguments, regardless of the number of parameters defined.
- Extra arguments are ignored, missing arguments are set to `undefined`.
- The `arguments` object (not available in arrow functions) contains all passed arguments.

Example:
```javascript
function example(a, b) {
    console.log(a, b, arguments);
}

example(1, 2, 3, 4);
// Output: 1 2 [1, 2, 3, 4]
```

## 7. Function Hoisting

Function declarations are hoisted to the top of their scope:

```javascript
hoisted(); // Works!

function hoisted() {
    console.log('I am hoisted');
}
```

Function expressions are not hoisted:

```javascript
notHoisted(); // Error!

var notHoisted = function() {
    console.log('I am not hoisted');
};
```

