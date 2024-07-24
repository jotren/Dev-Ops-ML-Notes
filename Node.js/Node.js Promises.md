# Node.js Promises #

A promise is when to take a function and execute different commands if the function was successful or not. They are a way to handle asynchronus operations. Can be easier to manage complex systems than callbacks. The benefits of promises are:

- Improves Code Readability
- Better handling of asynchronous operations
- Better flow of control definition in asynchronous logic
- Better Error Handling

To create a promise we first use the resolve and reject keyword. 

```javascript
const userLeft = true
const userWatchingCat = false

function examplePromise() {
  return new Promise((resolve, reject) => {
    if (userLeft) {
      reject('User is not watching')
    } else if (userWatchingCat) {
      reject('User is watching cat instead')
    } else {
      resolve('User is watching video')
    }
  })
}
```
When the if statements are fullfilled it will move to the resole, reject items. In order to view the result you then need to use the __then__ and __catch__ keywords.
```javascript
examplePromise().then(text => console.log(text)).catch(text => console.log(text))
```
Now this could also be achieved with call back functions
```javascript
function exampleCallback(cb, errorcb) {

  if (userLeft) {
    errorcb('User is not watching')
  } else if (userWatchingCat) {
    errorcb('User is wacthing cat instead')
  } else {
    cb('User is watching video')
  }
}

exampleCallback((cb) => {
  console.log(cb)
}, (errorcb) => {
  console.log(errorcb)
}) 
```
This seems relatively arbitrary at this level, but when you have complex systems of nested callbacks you can create something called callback hell. This is where you have tonnes of nested items which make the code very messy. Promises are easier to manage. Also remember new promises are like functions, need to use arrow notation to get them working.

You can also use the keyword __all__ to create an array of promises and then execute commands on them using the __then__ keyword.
```javascript
const recordOne = new Promise((resolve, reject) => {
  resolve('Video one has been recorded')
})

const recordTwo = new Promise((resolve, reject) => {
  resolve('Video one has been recorded')
})

const recordThree = new Promise((resolve, reject) => {
  resolve('Video one has been recorded')
})

Promise.all([recordOne, recordTwo, recordThree]).then(text => console.log(text))
```
This is another little program I created to help my understanding of promises
```javascript
promiseOn = false

const firstPromise = new Promise((resolve,reject) => {
  if (promiseOn) {
    resolve('Promises are on')
  } else {
    reject('Promises are off')
  }
})

const secondPromise = new Promise((resolve,reject) => {
  if (promiseOn) {
    resolve('Promises are on')
  } else {
    reject('Promises are off')
  }
})

const thirdPromise = new Promise((resolve,reject) => {
  if (promiseOn) {
    resolve('Promises are on')
  } else {
    reject('Promises are off')
  }
})

Promise.all([firstPromise, secondPromise, thirdPromise]).then(text => console.log(text)).catch(text => console.log(text))
```

This is an example of how you would execute a promise using the pokemon API:
```javascript
const pokemonAPI = 'https://pokeapi.co/api/v2/pokemon/'

const fetchPokemon = (id) => {
  fetch(pokemonAPI + id).then(res => res.json()).then(data => console.log(data.forms, data.stats))
}

fetchPokemon(347)
```