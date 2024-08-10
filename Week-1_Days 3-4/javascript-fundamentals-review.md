# JavaScript Fundamentals Review

## 1. Variables and Data Types

### Variable Declaration
```javascript
var oldWay = "I'm old school";  // Function-scoped, hoisted
let modern = "I'm block-scoped";  // Block-scoped, not hoisted
const constant = "I can't be reassigned";  // Block-scoped, can't be reassigned
```

### Data Types
- **Primitive Types**
  - Number: `let num = 42;`
  - String: `let str = "Hello";`
  - Boolean: `let bool = true;`
  - Undefined: `let undef;`
  - Null: `let nullValue = null;`
  - Symbol (ES6): `let sym = Symbol('unique');`
  - BigInt (ES11): `let bigInt = 1234567890123456789012345678901234567890n;`

- **Object**: `let obj = {key: 'value'};`
- **Array**: `let arr = [1, 2, 3];`

### Type Checking
```javascript
typeof 42;  // "number"
typeof "Hello";  // "string"
Array.isArray([1, 2, 3]);  // true
```

## 2. Operators

### Arithmetic Operators
```javascript
let a = 5 + 3;  // Addition
let b = 10 - 4;  // Subtraction
let c = 3 * 4;  // Multiplication
let d = 12 / 3;  // Division
let e = 10 % 3;  // Modulus (remainder)
let f = 2 ** 3;  // Exponentiation (ES7)
```

### Comparison Operators
```javascript
5 == "5";  // true (loose equality)
5 === "5";  // false (strict equality)
5 != "6";  // true
5 !== "5";  // true
5 > 3;  // true
5 >= 5;  // true
```

### Logical Operators
```javascript
true && false;  // false (AND)
true || false;  // true (OR)
!true;  // false (NOT)
```

## 3. Control Structures

### If Statement
```javascript
if (condition) {
    // code
} else if (anotherCondition) {
    // code
} else {
    // code
}
```

### Switch Statement
```javascript
switch (expression) {
    case value1:
        // code
        break;
    case value2:
        // code
        break;
    default:
        // code
}
```

### Loops
```javascript
// For loop
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// While loop
let i = 0;
while (i < 5) {
    console.log(i);
    i++;
}

// Do-while loop
let j = 0;
do {
    console.log(j);
    j++;
} while (j < 5);

// For...of loop (ES6, for iterables)
for (let item of [1, 2, 3]) {
    console.log(item);
}

// For...in loop (for object properties)
for (let key in {a: 1, b: 2}) {
    console.log(key);
}
```

## 4. Functions and Scope

### Function Declaration
```javascript
function greet(name) {
    return `Hello, ${name}!`;
}
```

### Function Expression
```javascript
const greet = function(name) {
    return `Hello, ${name}!`;
};
```

### Arrow Function (ES6)
```javascript
const greet = (name) => `Hello, ${name}!`;
```

### Scope
```javascript
let globalVar = "I'm global";

function exampleFunction() {
    let localVar = "I'm local";
    console.log(globalVar);  // Accessible
    console.log(localVar);   // Accessible
}

console.log(globalVar);  // Accessible
console.log(localVar);   // ReferenceError: localVar is not defined
```

### Closures
```javascript
function outerFunction(x) {
    return function(y) {
        return x + y;
    };
}

const addFive = outerFunction(5);
console.log(addFive(3));  // 8
```

