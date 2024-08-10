# Your First Node.js Application

## 1. Running JavaScript in Node.js REPL

REPL stands for Read-Eval-Print Loop. It's an interactive programming environment that allows you to execute JavaScript code line by line.

To start the Node.js REPL:
1. Open your terminal or command prompt.
2. Type `node` and press Enter.

You should see a `>` prompt. Now you can start typing JavaScript:

```javascript
> console.log('Hello, Node.js!')
Hello, Node.js!
undefined
> 2 + 2
4
> const greeting = 'Hello, World!'
undefined
> greeting
'Hello, World!'
> .exit
```

Use `.exit` or press Ctrl+C twice to exit the REPL.

## 2. Creating and Running a Simple Script

Let's create a simple Node.js script:

1. Create a new file named `app.js` in your project directory.
2. Open `app.js` in your text editor and add the following code:

```javascript
console.log('Hello from Node.js!');

const currentDate = new Date();
console.log(`The current date is ${currentDate.toDateString()}`);

for (let i = 1; i <= 5; i++) {
    console.log(`Counting: ${i}`);
}
```

3. Save the file.
4. In your terminal, navigate to the directory containing `app.js`.
5. Run the script with:

```
node app.js
```

You should see the output of your script in the terminal.

## 3. Understanding Modules and `require`

Node.js uses a module system to organize and reuse code. Let's create a simple module:

1. Create a new file named `greetings.js` with the following content:

```javascript
function sayHello(name) {
    return `Hello, ${name}!`;
}

function sayGoodbye(name) {
    return `Goodbye, ${name}!`;
}

module.exports = {
    sayHello,
    sayGoodbye
};
```

2. Now, let's use this module in our `app.js`. Modify `app.js` to look like this:

```javascript
const greetings = require('./greetings');

console.log(greetings.sayHello('Alice'));
console.log(greetings.sayGoodbye('Bob'));

// We can also use destructuring
const { sayHello, sayGoodbye } = require('./greetings');

console.log(sayHello('Charlie'));
console.log(sayGoodbye('David'));
```

3. Run `app.js` again:

```
node app.js
```

You should see the greetings printed in your terminal.

## 4. Using Built-in Modules

Node.js comes with several built-in modules. Let's use the `fs` (File System) module to read a file:

1. Create a text file named `message.txt` with some content.
2. Modify `app.js` to read this file:

```javascript
const fs = require('fs');

fs.readFile('message.txt', 'utf8', (err, data) => {
    if (err) {
        console.error('Error reading file:', err);
        return;
    }
    console.log('File contents:', data);
});

console.log('This will be printed before the file contents');
```

3. Run `app.js`:

```
node app.js
```

Notice how the last `console.log` is executed before the file contents are printed, demonstrating Node.js's asynchronous nature.

## Conclusion

This guide provides a hands-on introduction to creating your first Node.js application. Here are some additional points to consider:

1. **REPL Use Cases**: The REPL is great for quickly testing small snippets of code or exploring Node.js features and APIs.
2. **Script vs. Module**: Understanding the difference between a script (which you run directly) and a module (which you import into other files) is crucial in Node.js development.
3. **Module Patterns**: There are different ways to export functionality from a module. We used module.exports in the example, but you might also see exports.functionName = function() {...} in some code.
4. **ES Modules**: Node.js also supports ES modules (using import and export). To use them, you either need to use the .mjs file extension or set "type": "module" in your package.json.
5. **Asynchronous Nature**: The file reading example demonstrates Node.js's asynchronous nature. This is a fundamental concept in Node.js that you'll encounter frequently.
6. **Error Handling**: Notice how we handled potential errors in the file reading example. Proper error handling is crucial in Node.js applications.
7. **Global Objects**: In addition to modules, Node.js provides several global objects like console, process, and global. Exploring these can give you a better understanding of the Node.js environment.
8. **npm**: As you progress, you'll start using npm to install and manage third-party packages. This opens up a whole world of possibilities for your Node.js applications.

Remember, this is just the beginning. As you continue learning Node.js, you'll encounter more complex concepts like streams, events, and working with databases.
