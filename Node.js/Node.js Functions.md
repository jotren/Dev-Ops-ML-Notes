# Node.js Functions #

## Fuction Declaration ##

This function always starts with the keyword 'function'
```javascript
function sum(a, b) {
    console.log(a + b);
  }
  sum(5, 6);
```

## Function Expression ##  

In this function you are always declaring a value. These functions should be used inside of another function
other wise the program returns that the function has not been declared.
```javascript
const numbers = ([1, false, 5]).filter(function(item) {
    return typeof item === 'number';
  });
```

In the methods class we can have our expression function in a a method (:) or a callback (.reduce()). The reduce function is a built in javascript element that takes a function with arguments acc, num which stand for accumulator and number. The accumulator will be whatever is returned (i.e. the result of acc + num) and the number will be the next number in the array. There is a good video at this [link](https://www.youtube.com/watch?v=g1C40tDP0Bk)

```javascript
const methods = {
    numbers: [1, 5, 8],
    sum: function() { // Function expression
      return this.numbers.reduce(function(acc, num) { // func. expression
        return acc + num;
      });
    },
    count: function(array) { // Function expression
        return array.length;
      }
  }

 console.log(methods.sum())
 console.log(methods.count([1,2,3]));    // => 14
```

## Shorthand method definition ##

This method is structured in the same way but removes the work function making it shorthand
the benefit of using this way is shorter syntax and easier to track when debugging
```javascript
const collection = {
    items: [],
    add(...items) {
      this.items.push(...items);
    },
    get(index) {
      return this.items[index];
    }
  };
  collection.add('C', 'Java', 'PHP');
  console.log(collection.get(1)) // => 'Java'
```

## Arrow Function ##

This is the structure of an arrow function
```javascript
var absValue = (number) => {
    if (number < 0) {
      return -number;
    }
    return number;
  }
  console.log(absValue(-10)); // => 10
```
If there is only one parameter you can remove the brackets 
```javascript
var absValue = number => {
    if (number < 0) {
      return -number;
    }
    return number;
  }
  console.log(absValue(-10)); // => 10
```

If you are only calling one return you can omit the curly braces
```javascript
var absValue = number => -number
console.log(absValue(-10)); // => 10
```

If it is a function that takes no inputs you can just use brackets
```javascript
var absValue = () => -10
console.log(absValue(-10)); // => 10
```
The arrow function is very conveinent for short callbacks. The .some() function checks if any values in the array passes the test function
```javascript
var numbers_ = [1, 5, 10, 0];
numbers_.some(item => item === 0)
```

Which way is the best. Here are some rules on what functions to use: 

1. If the function uses this consistently throughout function use arrow
2. When the callback function has one short statement use the arrow function
3. When we are defining functions in a object literal (dictionary style class), use shorthard method (arrow function won't work) */

## Callback Functions ##

This is where in the function you put a parameter that is another function which needs to be executed syncronsly.

```javascript
function myCalculator(num1, num2, cb) {
  let sum = num1 + num2;
  cb(sum);
}

```
The cb is the parameter, which in the code then has cb(sum). In order to execute cb you need to put a funtion in the parameters. This will return an error
```javascript
myCalculator(1,2,3)

```
Because '3' is not a function. Instead you have to have something like this
```javascript
function myDisplay(something) {
  console.log(something)
}

myCalculator(1,2,myDisplay)
```

This way when the function is read the callback is excuted. You can also create a new function inside the myCalcualtor() function like so
```javascript
myCalculator(1,2,something => console.log(something))
```
This is useful because javascript is a single threaded code, it runs syncronsly. However in some cases you need a function to run directly after another function. If unclear run this code in the IDE and it should help 
```javascript
const firstFunction = () => {

  setTimeout(() => {
    console.log('firstFunction')
    //cb('Callback achieved')
  }, 3000)
}

const secondFunction = () => {
  console.log('SecondFunction')
}

firstFunction()
secondFunction()

const thirdFunction = cb => {

  setTimeout(() => {
    console.log('thirdFunction')
    cb('fourthFunction')
  }, 3000)
}

const fourthFunction = (something) => {
  var addition = ' which is coming last'
  console.log(something + addition)
}

thirdFunction(fourthFunction)
```
Notice that we are not using brackets when passing the fourthFunction. This is because we are not executing the function simply passing on its definition.

In this we have set the timeout function to recreate the fact that a kernel can sometimes take a while to execute when part of the event loop. Here is another good example using the pokemon API
```javascript
const namePokemon = (text) => {
  console.log(text)
}

const fecthPokemon = (id, cb) => {
  fetch('https://pokeapi.co/api/v2/pokemon/' + id)
  .then(res => res.json())
  .then(data => {
    cb(data)
    })
  }

```

## Error-First Callback ##

This is a convention of Node.js. The structure of the statement is as follows where
```javascript
const ErrorFirstCallback = (err, data) => {
  if (err) {
    return console.log(err);
  }
  console.log("Function successfully executed");
};

```
Where the first argument is reserved for the error object and the second argument is reserved for successful response data. This function recognises the err keyword in the arguments. 

## Built In Functions ##

There are a few built in functions such as:

```
.find()
```
