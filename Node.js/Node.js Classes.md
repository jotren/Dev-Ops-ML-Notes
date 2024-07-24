# Node.js Classes #
It is custom to keep classes as capitals!

Now in ES6 which is known as "next gen Javascript" a few updates were let and const instead of var, arrow functions and classes. Now classes are blueprints for objects that can have both properties __and__ methods. Firstly we create a class using the {} notation and give the class properties using the constructor function.
```javascript
class User{
  constructor(firstName, lastName){
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
```
We then assign a const/let/var to the class using the new keyword
```javascript
const new_user = new User('Jonah', 'Trenouth')
console.log(new_user)
```
Then when we console log we get the object properties we assigned. You can also define methods (functions) in our classes. This can be done with the shorthand method definition
 
 ```javascript
class User{
  constructor(firstName, lastName){
    this.firstName = firstName;
    this.lastName = lastName;
  }

  fullName() {
    return this.firstName + ' ' + this.lastName 
  }
}
```

Then when we call the newly created class and function the result will be a full name
```javascript
const new_user = new User('Jonah', 'Trenouth')
console.log(new_user.fullName())
```

## Class Inheritance ## 

In JS classes can inherit methods and properties and other classes. These are known as parent classes. You can use the keyword __extends__ to show the program you are amending the existing class. We then use the keyword __super__ to show that we are taking the constructor of the parent class. Note that the new class will also take on the methods of the parent class.
```javascript
class User{
  constructor(firstName, lastName){
    this.firstName = firstName;
    this.lastName = lastName;
  }
  fullName() {
    return this.firstName + ' ' + this.lastName
  }

}


class UserAge extends User {
  constructor(firstName, lastName, age) {
    super(firstName, lastName)
    this.age = age
  }
}
```
When you log this to the console the results should be clear.
```javascript
const new_userAge = new UserAge('Jonah', 'Trenouth', 28)
console.log(new_userAge)
console.log(new_userAge.fullName())
```
In order to change the elements within a class we use this code
```javascript
const new_userAge = new UserAge('Jonah', 'Trenouth', 28)
new_userAge.lastName = 'Trenoweth'
```
To return a certain element it works similarly to objects in python.
```javascript
console.log(new_userAge.lastName)
```
## Static Calls ##

If you wanted to call a function on the whole class rather then an instance you can use the keyword __static__. This can be useful when you want to return each instance of your class. e.g. 
```javascript
products = []

class Product {
  constructor(id, name) {
    this.id = id
    this.name = name
  }

  save() {
    products.push(this.name)
  }

  returnName() {
    return this.id + " " + this.name
  }

  static printProducts(){
    return products
  }
}

let product1 = new Product(1,'Jonah')
let product2 = new Product(2,'Dylan')
let product3 = new Product(3,'Finlay')  

product1.save()
product2.save()
product3.save()

console.log(products)
console.log(Product.printProducts())
```
Notice how the static function can be called on the product class rather than the product1 object.

