# ES6 Notes

## let and const

`var` variables are _global/function_ scoped

`let` and `const` are _block_ scoped. Block scope = contained in curly brackets { }.

### `let`

```js
for (var age = 1; age < 10; age++) {
    // runs after 200ms
    setTimeout(function() {
        console.log(`I am ${age} years old.`);
    }, 200);
}

// I am 10 years old.
// (9 times!)

console.log(age); // 10
```

`age` is globally scoped so by the time it gets console.log'd (because of setTimeout), it's been overwritten because it's the _same_ variable.

```js
for (let age = 1; age < 10; age++) {
    // runs after 200ms
    setTimeout(function() {
        console.log(`I am ${age} years old.`);
    }, 200);
}

/*
I am 1 years old.
I am 2 years old.
I am 3 years old.
I am 4 years old.
I am 5 years old.
I am 6 years old.
I am 7 years old.
I am 8 years old.
I am 9 years old.
*/

console.log(age); // ReferenceError: age is not defined
```

Essentially now that `age` is blocked scoped, each run through the loop `age` is like a new variable.

### const

`const` variables cannot be changed* (on purpose or by accident).

However, `const` **is not immutable**.

A `const` variable that's an array or an object -> Its properties/values can be updated/added/deleted. Being a `const`ant does not prevent this, it just prevents it from being changed through reassignment and redeclaration.

```js
const name = 'Adam';
name = 'Bob'; // TypeError: Assignment to constant variable.

const stuff = ['things','doodads'];
stuff.push('more things');
stuff.shift();
console.log(stuff); // [ 'doodads', 'more things' ]

const me = {
    name: 'Adam',
    age: 38
}

me.name = 'Bob';
me.hairColor = 'graying';

console.log(me); // { name: 'Bob', age: 38, hairColor: 'graying' }
```

## Spread Operator `...`

The spread operator `...` is used to _spread_ elements of an iterable collection (like an array or string) into both literal elements and individual function parameters.

```js
let params = [ 'hello world', true, 7 ];
let other = [ 1, 2, ...params ]; // [1, 2, 'hello world', true, 7]

function foo(x, y, ...a) {
    return (x + y) * a.length;
}
foo(1, 2, ...params); // returns 9

let str = 'foobar';
let chars = [ ...str ]; // [ "f", "o", "o", "b", "a", "r" ]
```

## Template Literals/Strings

```js
"Because everyone " + sucks + " at concatenation."
```

Use `Backticks` -> ` to enclose your string. Then use ${ } to enclose your variable. You can actually use any JavaScript inside the ${ } segment. Do calculations, even run a function!

```js
function catYears(age) {
    return age === 1 ? 15
         : age === 2 ? 24
         : 24 + (age - 2) * 4;
}

const name = 'Moonpie';
const age = 10;
const sentence = `My ${age} year old cat, ${name}, is ${catYears(age)} years old in cat years.`;

console.log(sentence); // My 10 year old cat, Moonpie, is 56 years old in cat years.
```

### HTML in JavaScript

This is very handy for HTML in JavaScript since these can be multi-line and not make your markup look unsettling.

```js
const person = {
    name: 'Adam',
    job: 'Developer',
    city: 'Dayton',
    bio: 'Adam is a developer of assorted things and a geek in things of the spheniscidae variety.'
};

const markup = `
    <div class="person">
        <h2>
            ${person.name} -
            <span class="job">${person.job}</span>
        </h2>
        <p class="location">${person.city}</p>
        <p class="bio">${person.bio}</p>
    </div>
`;

document.body.innerHTML = markup;
```

### Loops

```js
let cats = [
    { name: 'Mango', mood: 'lazy' },
    { name: 'Moonpie', mood: 'hungry' },
    { name: 'Macaroon', mood: 'hyper' }
];

var html = `
    <ul>
        ${cats.map(cat => `
            <li>${cat.name} is a ${cat.mood} cat.</li>
        `).join('')}
    </ul>
`;

/*
<ul>
    <li>
        Mango is a lazy cat.
    </li>
    <li>
        Moonpie is a hungry cat.
    </li>
    <li>
        Macaroon is a hyper cat.
    </li>
</ul>
*/
```

### `if` statements -> use a ternary operator

```js
const song = {
    name: 'Where is the Love?',
    artist: 'The Black Eyed Peas',
    year: 2003,
    featuring: 'Justin Timberlake'
}

const title = `<p>${song.name} (${song.year}) - ${song.artist} ${song.featuring ? `(Feat ${song.featuring})` : null}</p>`;

console.log(title); // <p>Where is the Love? (2003) - The Black Eyed Peas (Feat Justin Timberlake)</p>
```

## Default Function Arguments

Default values for function arguments

```js
function calculateBill(price, tax = 0.075, tip = 0.15) {
    return price + (price * tax) + (price * tip);
}

// use the defaults
calculateBill(100); // tax 7.5%, tip 15%

// specify your own values
calculateBill(100, .065, .20); // tax 6.5%, tip 20%

// specify one
calculateBill(100, undefined, .20); // tax 7.5%, tip 20%
```

## Arrow Functions

```js
let names = ['jake', 'ben', 'abi', 'nick'];
let fullnames = names.map(function(name){
    return `${name} romig`;
});
```

Replace `function` with `=>` and move to after the argument. So instead we can do:

```js
let names = ['jake', 'ben', 'abi', 'nick'];
let fullnames = names.map((name) => {
    return `${name} romig`;
});
```

Single arguments do not require the parenthesis around it.

```js
let fullnames = names.map(name => {
    return `${name} romig`;
});
```

### Implicit Return

If your function is a one-liner, you can just remove the `return` keyword and the curly brackets { }.

```js
let fullnames = names.map(name => `${name} romig`);
```

```js
// Multiple arguments
let numberedNames = names.map((name, index)) => `${name} is #${index + 1}`);

// No arguments
setTimeout(() => { console.log('done') }, 1000);
```

```js
// implicit return an object literal
let fullNames = names.map(name => ({first: name, last: 'romig'}) );

/*
[ { first: 'jake', last: 'romig' },
  { first: 'ben', last: 'romig' },
  { first: 'abi', last: 'romig' },
  { first: 'nick', last: 'romig' } ]
*/
```

### `this`

With arrow functions, the value of `this` does not change. `this` is equal to whatever the parent is bound to.

Problem:

```js
card.addEventListener('click', function() {
    // add closing class to start closing animation
    this.classList.add('closing');
    // after 1 second, add closed class
    setTimeout(function() {
        console.log(this); // this is 'window'
        this.classList.add('closed'); // Error: Cannot read proerty 'add' of undefined
    }, 1000);
});
```

Solution:

```js
card.addEventListener('click', function() {
    // add closing class to start closing animation
    this.classList.add('closing');
    // after 1 second, add closed class
    setTimeout(() => {
        console.log(this); // this is same as outside this callback
        this.classList.add('closed'); // Works!!
    }, 1000);
});
```

## Same Name Properties / Object Literals

Instead of:

```js
const cat = {
    first: first,
    last: last,
    age: age,
    breed: breed
}
```

do this:

```js
const cat = {
    first,
    last,
    age,
    breed
}
```

## Method Definition / Concise Methods

Old school:

```js
var modal = {
    create: function(selector) {
        // select
    },
    open: function(content) {
        // open
    },
    close: function() {
        // close
    }
}
```

new hotness:

```js
var modal = {
    create (selector) {
        // select
    },
    open (content) {
        // open
    },
    close () {
        // close
    }
}
```

## New Built-in Methods

### Array.find() & Array.findIndex()

```js
[ 1, 2, 4, 3].find(x => x > 3) // 4
[ 1, 2, 4, 3].findIndex(x => x > 3) // 2
```

### String Repeating

```js
"what".repeat(3) // "whatwhatwhat"
```

### String Searching

```js
"hello world".startsWith("ello", 1) // true
"hello world".endsWith("hello", 5)  // true
"hello world".endsWith("world")     // true
"hello world".includes("llo w")     // true
"hello world".includes("hello", 1)  // false
```

### Number Type Checking

```js
Number.isNaN(42)    // false
Number.isNaN("foo") // true

Number.isFinite(1234) // true
Number.isFinite(Infinity) // false
```

### Number Truncation

Drops the decimal from a float, leaving the integer. Does not round.

```js
Math.trunc(42.7) // 42
Math.trunc(0.1) // 0
```

### Number Sign Determination

```js
Math.sign(42) // 1
Math.sign(0)  // 0
Math.sign(-3) // -1
```

## Destructuring

Destructuring allows us to create and assign multiple variables in a single line of code. This works with both objects and arrays.

For example, we have this object:

```js
const person = {
    first: 'Adam',
    last: 'Romig',
    twitter: '@penguingeek',
    medium: '@adam.romig'
}
```

Say I want to create three variables called `first`, `last`, and `twitter`. We could do it like this:

```js
const first = person.first;
const last = person.last;
const twitter = person.twitter;
console.log(first, last, twitter); // Adam Romig @penguingeek
```

Ugh. So much typing. How about:

```js
const { first, last, twitter } = person;
console.log(first, last, twitter); // Adam Romig @penguingeek
```

Nested data

```js
const adam = {
    first: 'Adam',
    last: 'Romig',
    links: {
        social: {
            twitter: 'https://twitter.com/penguingeek',
            facebook: 'https://facebook.com/penguingeek'
        },
        web: {
            blog: 'https://medium.com/@adam.romig'
        }
    }
}

const { twitter, facebook } = adam.links.social;
// or even use a different variable name than the key
const { twitter:tweet, facebook:fb } = adam.links.social;
```

Arrays too! As long as you know the index order, of course.

```js
const details = ['Adam Romig', 42, 'penguin-geek.org'];
const [name, number, website] = details;
console.log(name, number, website);
```

### Default values if they don't exist

```js
const [name, number, website, fav_food = 'tacos'] = details;
console.log(fav_food); // tacos
```

### Swap Values

So annoying:

```js
var a = 1234;
var b = 5678;
var tmp;

tmp = a;
a = b;
b = tmp;
// a = 5678, b = 1234
```

Say goodbye to that nonsense:

```js
let a = 1234;
let b = 5678;

[a, b] = [b, a];
// a = 5678, b = 1234
```

### Return multiple values from a function

```js
function convertCurrency(amount) {
    const converted = {
        EUR: amount * 0.8071,
        GBP: amount * 0.7205,
        CAD: amount * 1.296,
        JPY: amount * 105.99,
        PHP: amount * 52.05
    };
    return converted;
}

const { EUR, CAD, JPY, PHP } = convertCurrency(100);
console.log(EUR, CAD, JPY, PHP); // 80.71000000000001 129.6 10599 5205
```

## Sets - The arrays you may have always wanted

A `Set` in ES6 is like unique array with a nice API for adding/removing/checking its items.

```js
const students = new Set();
students.add('Mac');
students.add('Mango');
students.add('Moonpie');
// or
const students = new Set(['Mac', 'Mango', 'Moonpie']);
```

Although it's only for unique data. That means you can't have duplicates of the same exact data.

```js
students.size; // 3
students.add('Mac');
students.size; // 3 - there aren't two entries of 'Mac', the above was ignored
```

```js
students.delete('Mac');
students.size; // 2
students.has('Mango'); // true
students.has('Mac'); // false (it was deleted)
students.clear(); // empty now
```

Any datatype can be added to a set and can mix datatypes.

```js
const superCat = { name: 'Super Cat', powers: 'Cat Stuff' };
students.add(superCat);
students.has(superCat); // true
```

### Looping through a Set

`for-of` loop

```js
for (const student of students) {
    console.log(student);
}
```

## ES7

Array Includes

```js
['a', 'b', 'c'].includes('a'); // true
['a', 'b', 'c'].includes('x'); // false
```

Exponential Operator

```js
let a = 2 ** 3; // 8
```