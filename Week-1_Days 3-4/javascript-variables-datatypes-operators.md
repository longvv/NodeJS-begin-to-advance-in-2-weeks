# JavaScript: Variables, Data Types, and Operators

## Variables

In JavaScript, variables are used to store data values. Here's how they work internally:

### Memory Allocation

1. When a variable is declared, JavaScript allocates memory for it.
2. The amount of memory allocated depends on the data type.

### Variable Declaration and Initialization

```javascript
let x;        // Declaration: memory allocated, value is undefined
x = 5;        // Initialization: value 5 is stored in the allocated memory
let y = 10;   // Declaration and initialization in one step
```

### Memory Management

JavaScript uses automatic memory management (garbage collection):
- Allocates memory when objects are created
- Frees memory when objects are no longer used

## Data Types

JavaScript has two categories of data types:

### Primitive Types

Stored directly in the location that the variable accesses.

1. **Number**: 64-bit floating point
   ```javascript
   let num = 42;
   ```
   Internally: Stored using IEEE 754 standard

2. **String**: Immutable sequence of characters
   ```javascript
   let str = "Hello";
   ```
   Internally: Stored as a sequence of 16-bit unsigned integer values

3. **Boolean**: true or false
   ```javascript
   let bool = true;
   ```
   Internally: Typically represented by a single byte

4. **Undefined**: Variable declared but not assigned a value
   ```javascript
   let undef;
   ```

5. **Null**: Intentional absence of any object value
   ```javascript
   let nullVar = null;
   ```

6. **Symbol**: Unique identifier
   ```javascript
   let sym = Symbol("id");
   ```

7. **BigInt**: Integers of arbitrary length
   ```javascript
   let bigInt = 1234567890123456789012345678901234567890n;
   ```

### Reference Types

Stored by reference, meaning the variable stores a pointer to the location in memory where the object is stored.

1. **Object**:
   ```javascript
   let obj = {name: "John", age: 30};
   ```
   Internally: Stored as a collection of key-value pairs

2. **Array**:
   ```javascript
   let arr = [1, 2, 3];
   ```
   Internally: Stored as objects with integer keys

3. **Function**:
   ```javascript
   let func = function() { return "Hello"; };
   ```
   Internally: A special type of object

## Type Coercion

JavaScript is a dynamically typed language and performs automatic type conversion:

```javascript
console.log("5" + 3);  // Outputs: "53" (number coerced to string)
console.log("5" - 3);  // Outputs: 2 (string coerced to number)
```

## Operators

Operators in JavaScript work differently based on the data types of their operands.

### Arithmetic Operators

```javascript
let a = 5 + 3;  // Addition
let b = 5 - 3;  // Subtraction
let c = 5 * 3;  // Multiplication
let d = 5 / 3;  // Division
let e = 5 % 3;  // Modulus
let f = 5 ** 3; // Exponentiation
```

Internally: These operations are performed on the binary representations of numbers.

### Comparison Operators

```javascript
5 > 3   // Greater than
5 < 3   // Less than
5 >= 3  // Greater than or equal to
5 <= 3  // Less than or equal to
5 == 3  // Equality with type coercion
5 === 3 // Strict equality without type coercion
```

### Logical Operators

```javascript
true && false  // Logical AND
true || false  // Logical OR
!true          // Logical NOT
```

These operators use short-circuit evaluation.

### Bitwise Operators

```javascript
5 & 3  // Bitwise AND
5 | 3  // Bitwise OR
5 ^ 3  // Bitwise XOR
~5     // Bitwise NOT
5 << 1 // Left shift
5 >> 1 // Sign-propagating right shift
5 >>> 1 // Zero-fill right shift
```

These operators work directly on the binary representations of numbers.

## Memory Model

- Primitive types are stored on the stack.
- Reference types are stored on the heap, with the variable holding a reference to the memory location.

```
Stack                   Heap
+-----------+           +-------------------+
| num: 42   |           | obj:              |
| str: "Hi" |           |   +-------------+ |
| obj: -----+---------->|   | name: "John"| |
+-----------+           |   | age: 30     | |
                        |   +-------------+ |
                        +-------------------+
```

