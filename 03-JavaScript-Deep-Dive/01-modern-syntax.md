# Modern JavaScript Syntax (ES6+)

## Introduction
Modern JavaScript (ES6+) introduces powerful features that make code more readable, maintainable, and efficient. This lesson covers the essential modern syntax features every JavaScript developer should master.

## Variable Declarations and Scoping

### let and const vs var
```javascript
// var - function scoped, hoisted, can be redeclared
function varExample() {
    console.log(x); // undefined (hoisted but not initialized)
    var x = 5;
    var x = 10; // Can be redeclared
    console.log(x); // 10
}

// let - block scoped, hoisted but not initialized (Temporal Dead Zone)
function letExample() {
    // console.log(y); // ReferenceError: Cannot access 'y' before initialization
    let y = 5;
    // let y = 10; // SyntaxError: Identifier 'y' has already been declared
    console.log(y); // 5
    
    if (true) {
        let z = 20; // Block scoped
        console.log(z); // 20
    }
    // console.log(z); // ReferenceError: z is not defined
}

// const - block scoped, must be initialized, cannot be reassigned
function constExample() {
    const PI = 3.14159;
    // PI = 3.14; // TypeError: Assignment to constant variable
    
    const person = {
        name: 'John',
        age: 30
    };
    
    person.age = 31; // OK - object properties can be modified
    // person = {}; // TypeError: Assignment to constant variable
}
```

**Code Explanation:**
- `var`: Function-scoped variable that is hoisted and can be redeclared
- `console.log(x)`: Returns `undefined` because var is hoisted but not initialized
- `let`: Block-scoped variable with Temporal Dead Zone (cannot be accessed before declaration)
- `const`: Block-scoped constant that must be initialized and cannot be reassigned
- `person.age = 31`: Object properties can be modified even when object is const
- **Temporal Dead Zone**: Period between entering scope and variable declaration where variable cannot be accessed

**Benefits:**
- `let` and `const` prevent common bugs caused by hoisting and scope issues
- Block scoping provides better variable isolation
- `const` prevents accidental reassignments
- Modern syntax is more predictable and maintainable

### Block Scope Examples
```javascript
// Block scope demonstration
function blockScopeDemo() {
    var functionScoped = 'I am function scoped';
    let blockScoped = 'I am block scoped';
    const alsoBlockScoped = 'I am also block scoped';
    
    if (true) {
        var functionScopedInBlock = 'Still function scoped';
        let blockScopedInBlock = 'Block scoped';
        const alsoBlockScopedInBlock = 'Also block scoped';
        
        console.log(blockScopedInBlock); // Works
        console.log(alsoBlockScopedInBlock); // Works
    }
    
    console.log(functionScopedInBlock); // Works - function scoped
    // console.log(blockScopedInBlock); // ReferenceError
    // console.log(alsoBlockScopedInBlock); // ReferenceError
}

// Loop with different variable declarations
function loopExample() {
    // var in loop - problematic
    for (var i = 0; i < 3; i++) {
        setTimeout(() => console.log('var:', i), 100); // Prints 3, 3, 3
    }
    
    // let in loop - correct
    for (let j = 0; j < 3; j++) {
        setTimeout(() => console.log('let:', j), 100); // Prints 0, 1, 2
    }
}
```

## Arrow Functions and This Binding

### Arrow Function Syntax
```javascript
// Traditional function
function traditionalFunction(a, b) {
    return a + b;
}

// Arrow function equivalent
const arrowFunction = (a, b) => a + b;

// Single parameter - parentheses optional
const square = x => x * x;

// No parameters - parentheses required
const greet = () => 'Hello World';

// Multiple statements - braces required
const complexArrow = (x, y) => {
    const sum = x + y;
    const product = x * y;
    return { sum, product };
};

// Returning object literal - parentheses required
const createPerson = (name, age) => ({ name, age });
```

### This Binding Differences
```javascript
// Traditional function - this depends on how it's called
const obj = {
    name: 'My Object',
    traditionalMethod: function() {
        console.log('Traditional:', this.name);
        
        // Nested function loses this context
        function nestedFunction() {
            console.log('Nested:', this.name); // undefined or global object
        }
        nestedFunction();
        
        // Arrow function preserves this context
        const arrowNested = () => {
            console.log('Arrow nested:', this.name); // 'My Object'
        };
        arrowNested();
    },
    
    // Arrow function - this is lexically bound
    arrowMethod: () => {
        console.log('Arrow method:', this.name); // undefined or global object
    }
};

obj.traditionalMethod();
obj.arrowMethod();

// Event handlers - arrow functions preserve this
class EventHandler {
    constructor() {
        this.count = 0;
    }
    
    setupTraditional() {
        document.getElementById('btn1').addEventListener('click', function() {
            // this refers to the button element, not EventHandler instance
            console.log('Traditional handler:', this);
        });
    }
    
    setupArrow() {
        document.getElementById('btn2').addEventListener('click', () => {
            // this refers to EventHandler instance
            this.count++;
            console.log('Arrow handler count:', this.count);
        });
    }
}
```

## Template Literals and String Manipulation

### Basic Template Literals
```javascript
// String interpolation
const name = 'John';
const age = 30;
const message = `Hello, my name is ${name} and I am ${age} years old.`;

// Multi-line strings
const multiLine = `
    This is a multi-line string.
    It preserves whitespace and line breaks.
    Perfect for HTML templates or SQL queries.
`;

// Expression evaluation
const a = 10;
const b = 20;
const calculation = `${a} + ${b} = ${a + b}`; // "10 + 20 = 30"

// Function calls in templates
function formatCurrency(amount) {
    return `$${amount.toFixed(2)}`;
}

const price = 99.99;
const priceMessage = `Total: ${formatCurrency(price)}`; // "Total: $99.99"
```

### Tagged Template Literals
```javascript
// Tagged template function
function highlight(strings, ...values) {
    return strings.reduce((result, string, i) => {
        const value = values[i] ? `<mark>${values[i]}</mark>` : '';
        return result + string + value;
    }, '');
}

const user = 'John';
const score = 95;
const highlighted = highlight`User ${user} scored ${score} points!`;
// Result: "User <mark>John</mark> scored <mark>95</mark> points!"

// SQL query builder
function sql(strings, ...values) {
    return strings.reduce((query, string, i) => {
        const value = values[i] ? `'${values[i]}'` : '';
        return query + string + value;
    }, '');
}

const tableName = 'users';
const userId = 123;
const query = sql`SELECT * FROM ${tableName} WHERE id = ${userId}`;
// Result: "SELECT * FROM 'users' WHERE id = '123'"
```

## Destructuring Assignments

### Object Destructuring
```javascript
// Basic object destructuring
const person = {
    name: 'John Doe',
    age: 30,
    city: 'New York',
    country: 'USA'
};

const { name, age } = person;
console.log(name, age); // "John Doe" 30

// Destructuring with different variable names
const { name: fullName, age: years } = person;
console.log(fullName, years); // "John Doe" 30

// Default values
const { name, age, occupation = 'Developer' } = person;
console.log(occupation); // "Developer"

// Nested destructuring
const user = {
    id: 1,
    profile: {
        firstName: 'John',
        lastName: 'Doe',
        address: {
            street: '123 Main St',
            city: 'New York'
        }
    }
};

const { 
    profile: { 
        firstName, 
        lastName, 
        address: { city } 
    } 
} = user;

console.log(firstName, lastName, city); // "John" "Doe" "New York"

// Rest operator in destructuring
const { name, age, ...rest } = person;
console.log(rest); // { city: 'New York', country: 'USA' }
```

### Array Destructuring
```javascript
// Basic array destructuring
const colors = ['red', 'green', 'blue'];
const [first, second, third] = colors;
console.log(first, second, third); // "red" "green" "blue"

// Skipping elements
const [first, , third] = colors;
console.log(first, third); // "red" "blue"

// Default values
const [first = 'unknown', second = 'unknown'] = [];
console.log(first, second); // "unknown" "unknown"

// Rest operator
const [first, ...rest] = colors;
console.log(first); // "red"
console.log(rest); // ["green", "blue"]

// Swapping variables
let a = 1;
let b = 2;
[a, b] = [b, a];
console.log(a, b); // 2, 1

// Function return values
function getCoordinates() {
    return [10, 20];
}

const [x, y] = getCoordinates();
console.log(x, y); // 10, 20
```

### Parameter Destructuring
```javascript
// Function parameter destructuring
function greetUser({ name, age, city = 'Unknown' }) {
    return `Hello ${name}, age ${age}, from ${city}`;
}

const user = { name: 'John', age: 30 };
console.log(greetUser(user)); // "Hello John, age 30, from Unknown"

// Array parameter destructuring
function processCoordinates([x, y, z = 0]) {
    return `Position: (${x}, ${y}, ${z})`;
}

const coords = [10, 20];
console.log(processCoordinates(coords)); // "Position: (10, 20, 0)"

// Mixed destructuring
function processUserData({ name, scores: [math, science, english] }) {
    return `${name}: Math=${math}, Science=${science}, English=${english}`;
}

const student = {
    name: 'Alice',
    scores: [95, 87, 92]
};
console.log(processUserData(student)); // "Alice: Math=95, Science=87, English=92"
```

## Spread and Rest Operators

### Spread Operator
```javascript
// Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Object spreading
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }

// Function arguments
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15

// Copying arrays and objects
const originalArray = [1, 2, 3];
const copiedArray = [...originalArray];

const originalObject = { x: 1, y: 2 };
const copiedObject = { ...originalObject };

// Shallow copy - nested objects are still referenced
const nested = { a: { b: 1 } };
const shallowCopy = { ...nested };
shallowCopy.a.b = 2;
console.log(nested.a.b); // 2 (original is modified)
```

### Rest Operator
```javascript
// Rest in function parameters
function logArguments(first, second, ...rest) {
    console.log('First:', first);
    console.log('Second:', second);
    console.log('Rest:', rest);
}

logArguments(1, 2, 3, 4, 5);
// First: 1
// Second: 2
// Rest: [3, 4, 5]

// Rest in destructuring
const [first, second, ...remaining] = [1, 2, 3, 4, 5];
console.log(first, second, remaining); // 1, 2, [3, 4, 5]

const { name, ...otherProps } = { name: 'John', age: 30, city: 'NYC' };
console.log(name, otherProps); // "John" { age: 30, city: 'NYC' }
```

## Enhanced Object Literals

### Shorthand Properties and Methods
```javascript
// Shorthand property names
const name = 'John';
const age = 30;
const person = { name, age }; // Equivalent to { name: name, age: age }

// Shorthand method names
const calculator = {
    add(a, b) {
        return a + b;
    },
    
    subtract(a, b) {
        return a - b;
    },
    
    // Traditional method syntax still works
    multiply: function(a, b) {
        return a * b;
    }
};

// Computed property names
const propName = 'dynamic';
const obj = {
    [propName]: 'value',
    [`${propName}Key`]: 'another value',
    [Symbol.iterator]: function* () {
        yield 1;
        yield 2;
        yield 3;
    }
};
```

## Exercise: Modern JavaScript Practice

Create a user management system using modern JavaScript features:
- Use const/let appropriately for variable declarations
- Implement arrow functions for event handlers
- Use template literals for dynamic content generation
- Apply destructuring for function parameters and object handling
- Use spread operator for array manipulation
- Implement enhanced object literals for data structures

## Key Takeaways
- Use const by default, let when reassignment is needed
- Arrow functions preserve lexical this binding
- Template literals provide powerful string interpolation
- Destructuring makes code more readable and concise
- Spread operator enables functional programming patterns
- Modern syntax makes JavaScript more expressive and maintainable
