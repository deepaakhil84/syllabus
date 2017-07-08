![](https://img.shields.io/badge/status-draft-darkred.svg)
# JavaScript Core 6
** What we will learn today?**
- Callbacks
- Value Vs reference types
- Scoping
- this
---

- OO, Object creation, constructors, new keyword
- Inheritance?
- Functional programming (.map .filter etc..P)
- Promises?
- ES6?
---

## Built in constructor functions and methods

In the first lesson we learnt about some of the data types available in JavaScript: 'String', 'Boolean', 'Array', 'Object' and 'Number'.

So far we've learnt how to create data using literal syntax. For example to create a string you can write

```js
var myString = 'Hello';
```

And to create an array:

```js
var myArray = ['hello', 1, 2];
```

And an object, just for fun:

```js
var myObject = {
  name: 'Daniel',
  age: 10,
  interests: ['JavaScript', 'Cycling']
};
```

This is the best way to create new data, but it's helpful to know that you can also create new data using the `new` operator and a built in constructor function.

For example, you _could_ also create a new string like this:

```js
var myString = new String('Hello');
```

Or an array:

```js
var myArray = new Array(1,2,3);
```

> Do not actually create data using this approach in practice. These examples are for learning purposes only!

`String` is a constructor function just like the constructor functions we learnt to write in the previous session, and `myString` is an instance of `String`. (Kinda cool how JavaScript is built on JavaScript, huh?!)

When you check what a string looks like created in this way, things get a bit weird though:

```js
var myString = new String('Hello');
console.log(myString);
```

Will log:

```js
String {
  0: "h",
  1: "e",
  2: "l",
  3: "l",
  4: "o",
  length: 5,
  [[PrimitiveValue]]: "hello"
}
```

Whoa! What happened to my string?! Why does this happen? Why isn't `myString` just `'hello'`?

The first reason is that, if you remember back to the previous session, constructor functions create objects. Calling `new <Constructor>` always returns an object.

The second reason is that any objects a constructor creates can be given methods, and methods for built in data types turn out to be extremely useful.

> Recap: methods are functions that belong to an object

We can try calling some of the methods given to strings.

```js
myString.toUpperCase(); // hello
myString.split(''); // ['h', 'e', 'l', 'l', 'o']
myString.endsWith('o'); // true
```

You might also have noticed that our instance of `String` also has a `length` property, so we can go ahead and find out the length of our string:

```js
myString.length; // 5
```

Before we move onto the next section, just remember that the purpose of this lesson was to improve our understanding of how JavaScript work. You don't need to actually create data using `new String()` to get access to all the methods provided by `String` (JavaScript does this for you behind the scenes).


**Exercises**:

- Create a string using literal syntax and call the `.toUpperCase()` method on it
- Create an array and call the `.push()` method on it
- Create a string using the built in `String` constructor function and note any differences from using literal syntax
- Call a method e.g. `.toUpperCase()` on a string you created using the `String` constructor function

Learn about split and join

## Inheritance using Constructor.prototype

In the previous lesson we learnt how to write methods like this:

```
function Person(name) {
  this.name = name;
  this.greeting = function greeting() {
    alert('Hello my name is ' + this.name);
  }
}
```

Using this approach, every time that `Person` is instantiated, a new `greeting` function is created. If you create a `new Person()` 100 times, 100 `greeting` functions will be created for each one. This seems wasteful considering all the `greeting` functions are exactly the same.

One way to solve this is using _prototypal inheritance_. That sounds a bit scary so let's take it a step at a time.

We can say that a property is inherited when it is passed down from a parent. For example, perhaps one of the properties your parents passed to you was being really good looking. You might say that it's a family trait.

In JavaScript, there are 2 ways that a new objects receives properties. The first is _copying_, the way we've being doing it so far: inside the constructor function properties are added to the new object (`this`) when it is created. The properties are inherited from the (parent) constructor function, but once the object has been created the constructor function cannot change it.

### `prototype`

The other way that objects can receive properties from their constructor function is by _inheritance_. This happens via the constructor function's `prototype`, which is an object containing hidden properties and methods.  

> Exercise

> Open up the console in your web browser and type in `String.prototype`. An object with will be returned. If you expand this object you'll see that it contains several hidden methods.

> You can also search on the internet for "String prototype", "Array prototype" etc. and find documentation for the associated methods.

When you create a new object, a hidden link is made between the new object and it's constructor function's `prototype`.

We can test that this link exists in our browser's console:

```js
function MyConstructor () {}
var myObj = new MyConstructor();
Object.getPrototypeOf(myObj) === MyConstructor.prototype; // true
```

Let's add a method to our constructor function's prototype:

```js
MyConstructor.prototype.greeting = function () {
  console.log('Hello');
}
```

The `greeting` method will now be inherited by objects created with `MyConstructor`.

```js
var myObj2 = new Constructor();
myObj2.greeting(); // Hello
```

Remember that when objects are created a link between them and their constructor is created? Well, even though `myObj` was created before we added the `greeting` method, `myObj` is linked to `MyConstructor.prototype`, so it will inherit the newly created method as well.

```js
myObj.greeting(); // Hello
```

Both `myObj` and `myObj2` inherit properties from `MyConstructor` via _prototypal inheritance_.

```js
Object.getPrototypeOf(myObj) === Object.getPrototypeOf(myObj2) === MyConstructor.prototype; // true
```

### The prototype chain

When you access a property of an object, JavaScript first checks if the object has such a property itself. If it does it will use that. If not, it will check the object's prototype.

```js
function Person (name, formal) {
  this.name = name;
  if (formal) {
    this.greeting = function () {
      console.log('Hello, my name is ' + this.name);
    }
  }
}

Person.prototype.greeting = function () {
  console.log('Hey, it\'s ' + this.name);
}

var alice = new Person('Alice');
// No greeting method on alice
console.log(alice); // { name: 'Alice' }
alice.greeting(); // Hey it's Alice

var bob = new Person('Bob', true);
// bob was instantiated with a greeting method
console.log(alice); // { name: 'Bob', greeting: function() {} }
bob.greeting(); // Hello, my name is Bob
```

## Array methods and callbacks

The most useful built in methods in JavaScript are the `Array.prototype` methods. Every array has access to these methods (because Arrays are created by the `Array` constructor function they inherit the the properties of `Array.prototype`).

Of particular use are the `Array` methods that enable you to _iterate_ over an array. We've covered iteration already when we learnt how to use `for` loops. Well, I have some amazing news for you - you will probably never need to write a `for` loop again!

Let's take a look at `Array.prototype.forEach` first. This is pretty much a straight replacement for `for` loops.

```js
var myArr = [1,2,3];

myArr.forEach(function (item) {
  console.log(item);
});

// 1
// 2
// 3
```

OOOOF! Wasn't that glorious?! Much nicer than writing a `for` loop! So how exactly does this work? `forEach` _iterates_ over each item the array. For each item is calls a function - the one that was provided as an parameter to `forEach`.

### Callback functions

Functions that are provided as parameters to another function are called _callback functions_. Often we will write these functions inside the function call like did in the `forEach` example, but we can also pass in reference to an already defined function:

```js
function callback(item) {
  console.log(item);
}

myArr.forEach(callback);
```

Whether you define your function beforehand or write it straight into the function call will depend on whether you intend on re-using that callback again. 

### More array methods

.map()
.filter()

### Chaining methods

```js
var transformedArr = myArr
  .sort()
  .map(function (item) {
    return item * 2
  })
  .filter(function (item) {
    return item < 10
  }));
```

Reusuable callbacks.

```js
var transformedArr = myArr
  .sort()
  .map(double)
  .filter(lessThan10);
```

# Resources
1. https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance
2. http://sporto.github.io/blog/2013/02/22/a-plain-english-guide-to-javascript-prototypes/
3. https://johnresig.com/apps/learn/#64

# Homework

1. First part of Homework - Easy

2. Second part of Homework - Intermediate/Hard

3. **[Bonus/Optional]** Third part/optional - Hardest

4. **Research:**

## Prepare for the next class
1. Read this [Some Tutorial or Video etc...](https://google.com)