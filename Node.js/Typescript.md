# Typescript #
## Compiler vs. Interpreter ##
On thing that we need to understand is the difference between a compiler and an interpreter.

A compiler translates an entire program from its source code into machine code in a single pass, generating an executable file that can be run independently of the original source code. An interpreter, on the other hand, reads and executes each line of source code one at a time, without generating an executable file.

Compiled programs tend to execute faster than interpreted programs, but interpreted programs are generally more portable and better for debugging. Incase you were wondering python in an interpreter language.

## Typescript Overview ##

Typescript is built on javascript and essentially helps when building applications to make sure the type of object and classes that you are sharing is the same. This is address the shortcomings of javascript. 

There are two types of languages:

- Statically-typed: C#, C++, Java you can declare the variable type only once 

- Dynamically types: Javascript, python, Ruby, you can delcare the varibale type multiple times. I.e you can have a string that later becomes a number. 

Reasons why you would use typescript:

1. Strong Typing: TypeScript adds type annotations to variables, functions, and other objects. This makes it easier to catch errors before runtime, improving code quality and reducing debugging time.

2. Better IDE support: Since TypeScript includes type information, IDEs can provide more accurate autocompletion, refactoring, and error detection.

3. Increased scalability: TypeScript is especially useful for larger projects with multiple developers. Strong typing and other features make it easier to write maintainable, scalable code.

4. Compatibility with JavaScript: TypeScript is a superset of JavaScript, which means that any JavaScript code is also valid TypeScript code. This makes it easy to adopt TypeScript in an existing codebase.

There are some drawbacks in typescript which should be considered:

1. Learning Curve: TypeScript introduces additional syntax and concepts that may take some time for developers to learn, especially for those who are not familiar with strong typing.

2. Compilation Overhead: TypeScript needs to be compiled into JavaScript before it can be run in a browser or a Node.js environment. This adds an extra step to the development process and may slow down the development cycle.

3. Third-Party Library Compatibility: Not all third-party libraries may be compatible with TypeScript, which can create integration issues.

4. Type Annotations Can Be Tedious: While strong typing can be helpful in catching errors, adding type annotations to every variable, function, and object can be tedious and time-consuming.

5. Code Overhead: TypeScript requires additional code for type annotations, which can increase the size of the codebase and make it more difficult to read and maintain.

However, Tom seems to believe that the benefits are worth it.

In order to install typescript you use npm install:

```javascript
npm install -g typescript
```

In order to then run a tsc file we need to create a file with the .tsc extension. This is a typescript file and when we run the command


```javascript
tsc index.ts
```
The typescript compiler will then read this file and convert this into a javascript file index.js. We can then run our javascript file as normal. Now, to illustrate the beauty of typescript we can look at a simple bit of code.

```javascript
let age: number = 20;
age = 'a' 
```
First we delcare the the variable as a number and then set a string value. In javascript this would be picked up as we execute the file. In typescript this is picked up at compile

## Typescript Config File ##

The below code will create a config file with the following settings:
```javascript
tsc --init
```
This config file has a huge number of inputs, nobody understands all of the inputs.

There are some important ones to remeber:

```javascript

"target": "ES2016", //This tells the JS compiler what version of JS to convert into machine code

"module": "commonjs", //This will be covered later 
"rootDir": "./src", //This tells the compiler where the index.ts file will be stored which should be in a new folder called src (source)

"outDir": "./dist", //This informs the user where the JS files are kept

"removeComments": true, //This tells the program to remove all comments at compiling

"noEmitOnError": true, //Any mistakes and the compiler will not generate files

"sourceMap": true, //Allows us to use the debugger. Will create a file called index.js.map for the debugger 

 "allowJs": true,  //Allows us to convert the file into a js file type

"noUnusedParameters": true, //

"noImplicitReturns": true, //Unused function elements

"noUnusedLocals": true, //These three functions stop any unused or undeclared values in a function

```
With all this configuration if we run the command
```javascript
tsc
```
We will run the compiler and have the javascript files in dist.

When we are install packages, we need to use a different terminal command to pull the typesscript specific package. 

```bash
npm i --save-dev @types/express
npm i --save-dev @types/body-parser
``` 

Bear in mind you have to put --save-dev as this code will be modfied into normal javascript. This will return the relevant package.
## Built-In Types ##

When you declare a type in typescript, VSC is able to store this in memory and even when you remove the designator the type will be remembered:

```javascript
let sales: number = 123_456_789; -> let sales = 123_456_789;  //Will still remember type

let level -> let level //Will not remember type, assumed any type

```
The typescript any is assumed if you do not delcare anything. If we use any type we lose the whole reason we are using typescript.

IN order to show the program that you are using arrays we use the commands. Typescript will always assume any array if you do not declare.

```javascript
let numbers: number[] = [1, 2, 3]
```

Here is an example of how to use the forEach command in arrays for typescript:

```javascript
let numbers: number[] = [1, 2, 3]

let strings: string[] = []

numbers.forEach((number) => {
    strings.push(String(number))
})

console.log(strings)
```

It is worth noting that you have to run the tsc command and the node 'filename' command otherwise it won't execute. You can also have a tuple (fixed length array) with multiple types with the following:

```javascript
let user: [number, string] = [1, 'Mosh']

user[1] //When you can the second element it will recognise that it is a number 
```
Tuples are useful when we have key value pairs but get complicated with anything else. Enums are a way to designate values to a constant and a concise way:

```javascript
const enum Size { Small = 1, Medium, Large }; //Medium will now be 2 and 3
let mySize: Size = Size.Medium
console.log(mySize)
```
When we print mySize we will get back Medium. WE have declared the mySize type to be Size. 

This is quite a good example of how to use a function properly with the correct naming conventions.
```javascript
function calculateTax(income:number, taxYear=2022) {
    if ( taxYear < 2022 ) 
        var result:number = income*1.2;
    else 
        result = income*1.3;

    console.log(result)

}

calculateTax(10000)
```
When we are declaring objects in typescript we use something called a type alias:

```javascript
type Employee = {
    readonly id:  number, 
    name: string,
    retire: Date
}

let employee: Employee = { id: 1, name: 'Mosh', retire: new Date('1990-01-01')} //how you declare dates

console.log(employee.retire)
```
The concept of __narrowing__ is when you are not sure what type your variables are (either a string or a number).
```javascript
function kgToLbs(weight: number | string): number {
    //Narrowing
    if (typeof weight === 'number')
        return weight*2.2;
    else 
        return parseInt(weight)*2.2;
}

console.log(kgToLbs(1000))

console.log(kgToLbs('4000'))
```

__Intersection__ can be used when attempting to merge to distinct types in Node.js. For example you could have a person and department class that you want to merge into a new class.
```javascript
type Person = {
  name: string;
  age: number;
};

type Employee = {
  id: number;
  department: string;
};

type PersonEmployee = Person & Employee;

const employee: PersonEmployee = {
  name: 'John Doe',
  age: 35,
  id: 1234,
  department: 'Sales'
};
```

A literal can be used to limit the number of elements in a type. In the below example the values can only be red, blue and green

type Color = 'red' | 'green' | 'blue';
```javascript
type Color = 'red' | 'green' | 'blue';

function setBackgroundColor(color: Color) {
  document.body.style.backgroundColor = color;
}

setBackgroundColor('red'); // OK
setBackgroundColor('green'); // OK
setBackgroundColor('blue'); // OK
setBackgroundColor('yellow'); // Error: Argument of type 'string' is not assignable to parameter of type 'Color'.
```
Using literal types like this can help catch errors before your code even runs, and make your code more self-documenting and easier to read.

Typescript is very strict on null values, if you need to deal with null values you can use the below code:
```javascript
function greet(name: string | null | undefined) {
    if (name) 
        console.log(name.toUpperCase())
    else
        console.log('Hola')
}

greet(null)
```
In this specific case the code will return 'Hola' as the value was null.

__Optional Chaining__ is a check we put in place to ensure that the code won't error out
```javascript
type Person = {
  name: string;
  age?: number;
  address?: {
    street: string;
    city: string;
    state: string;
  };
};

const person1: Person = {
  name: 'John',
  age: 30,
  address: {
    street: '123 Main St',
    city: 'Anytown',
    state: 'CA'
  }
};

const person2: Person = {
  name: 'Jane'
};

console.log(person1.address?.city); // Output: Anytown
console.log(person2.address?.city); // Output: undefined
```

In this example we have got the "?" symbol which highligts that the variables are optional. When we console log the address of the second individual because we didn't declare it jsut returns an undefined value rather than an error.