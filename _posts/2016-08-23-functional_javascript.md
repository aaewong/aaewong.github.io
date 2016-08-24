---
layout: post
title: Functional Programmin in JavaScript - Arrays
---
One of my favorite parts of JavaScript is its functional capabilities. As we discussed in my last post, JavaScript functions are first class objects, meaning that they can be passed around as variables, even to other functions. In this post, we'll look at some of the ways we can utilize JavaScript's functional features with the ```Array.prototype``` for cleaner code with reduced [side-effects](https://en.wikipedia.org/wiki/Side_effect_(computer_science)).

With a JavaScript array, we can iterate through its elements in a particular manner by executing a callback function on each of its elements. Let's take a look at the following:

* forEach()
* map()
* reduce()
* filter()
* some()


## [forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 
The ```forEach``` function is perhaps the most straightforward. It calls a function on each of the elements in the array it belongs to.

Let's say we have an array of strings that we want to print to the console.

```javascript
var myArray = ["one", "two", "three"];
```

Here's how we would do it with a typical JavaScript ```for``` loop:

```javascript
for (let i = 0; i < myArray.length; i++) {
    console.log(myArray[i]);
}
// ["one", "two", "three"]
```

Using ```forEach``` it would like this:

```javascript
myArray.forEach(function(element) {
    console.log(element);    
});
// ["one", "two", "three"]
```

Using ES6 [arrow function syntax](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions), we could even write this in one line:

```javascript
myArray.forEach(element => console.log(element)); 
// ["one", "two", "three"]
```

In the examples above, we're passing an anonymous callback function to ```forEach``` that logs the input argument to the console. 

This approach is more readable and less error prone than the previous example. It tells the reader that we're iterating through ```myArray``` and for each element, we will perform this particular operation. In many use cases, a for loop requires writing extra code that isn't imporant to us (incrementing the index, looping condition, array size, etc).

Alternatively, we could pass a function variable as the callback function:

```javascript
var print = function(element) {
    console.log(element);
};

myArray.forEach(print); // ["one", "two", "three"]
```


## [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

This function returns a new array created by running the callback function on each element in the same order. It's particularly useful for converting one list of values to another kind of list.

Let's convert a list of objects containing a first and last name, and convert it into a list of strings containing the full name of each student:

```javascript
var students = [{firstName: "John", lastName: "Smith"}, 
    {firstName: "Jane", lastName: "Doe"}];

var fullNames = students.map(function(student) {
    return student.firstName 
        + " " + student.lastName;
});

console.log(fullNames); // ["John Smith", "Jane Doe"]
```

Using a for loop, it would look like this:

```javascript
var fullNames = [];
for (let i = 0; i < students.length; i++) {
    fullNames.push(students[i].firstName + 
        " " + students[i].lastName);
}
```


## [reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

Reduce is a bit more trickier to understand, but is very powerful. It applies the callback function left-to-right on each value of the array to reduce it to a single function.

Summing an array:

```javascript
[1, 2, 3, 4, 5].reduce(function(prev, curr) {
    return prev + curr;
});
// => 15
```

The callback function returns an accumulator, which result of the callback operation with the current value.

Reduce also takes an initial accumulator value as the second parameter, which we'll use in the next example to flatten an array of arrays:

```javascript
[[1, 2], [3, 4], [5, 6]].reduce(function(prev, curr) {
    return prev.concat(curr);
}, []);
// => [1, 2, 3, 4, 5, 6]
```

We could do something similar to merge a list of objects:

```javascript
var a = {a: 1}, 
    b = {b: 2},
    c = {c: 3};

var merge = function(prev, curr) {
    var obj = prev;
    Object.keys(curr).forEach(function(key) {
        obj[key] = curr[key];
    });
    return obj;
};

[a, b, c].reduce(merge, {});
// => {a: 1, b: 2, c: 3}
```


## [filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

Another useful function is ```filter```, which returns a new array with elements for which the boolean expression returned the callback evaluates to true.

Filtering out odd numbers:

```javascript
[1, 2, 3, 4, 5].filter(num => {return num % 2 === 0}); // => [2, 4]
```

Cities outside Canada:

```javascript
var cities = [{name: 'Vancouver', country: 'Canada'}, 
    {name: 'Toronto', country: 'Canada'}, 
    {name: 'Seattle', country: 'USA'}, 
    {name: 'Tokyo', country: "Japan"}];

var isOutsideCanada = function(city) {
    return city.country !== "Canada";
};

cities.filter(isOutsideCanada); 
// => [{name: 'Seattle', country: 'USA'}, {name: 'Tokyo', country: "Japan"}]
```


## [some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

Unlike the other functions we've looked at so far, ```some``` returns a boolean. It returns and stops the iteration when the first callback function evaluates to true.

```javascript
[5, 6, 7, 9].some(function(element) {
    console.log(element);
    return element === 7;
});
// 5
// 6
// 7
// => true  
```


Some people like to use ```some``` to short circuit ```forEach```. Personally, I don't recommend this, as ```some``` should be used only be used to check for the presence of a condition within a list. If you need to break out of an array, use a regular ```for``` loop or a combination of other array functions.

## Method chaining

Since some of these functions return arrays, we can chain functions together. For example, let's find the total population of the Canadian metropolitan areas in millions from array below:

```javascript
var cities = [
    {name: "Vancouver", population: 2313328, country: "Canada"}, 
    {name: "Toronto", population: 5583064, country: "Canada"}, 
    {name: "Seattle", population: 3733580, country: "USA"}, 
    {name: "Montreal", population: 3824221, country: "Canada"},
    {name: "San Francisco", population: 7150739, country: "USA"}, 
];

var isCanadian = (city) => city.country === "Canada";
var populationInMillions = (city) => city.population / 1000000;
var sum = (prev, curr) => prev + curr;

cities.filter(isCanadian)
      .map(populationInMillions)
      .reduce(sum);
// => 11.720613
```

## Wrap up

In this post, we learned how to use ```forEach```, ```map```, ```reduce```, ```filter```, and ```some``` to perform operations over arrays. This way of coding helps communicates code behaviour and reduces the amount of code we need to write.