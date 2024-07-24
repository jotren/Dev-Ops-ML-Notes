# Node.js SQL Overview # 

Node.js has a number of different ways to connect to SQL. One can be by installing the mysql2 package

```
npm install --save mysql2
```
We can then connect to our SQL database with the following commend

```javascript
const mysql = require('mysql2')

const pool = mysql.createPool({
    host: 'localhost',
    user: 'root',
    database: 'nlp_database',
    password: 'Motorstorm1'
})

module.exports = pool.promise()
```

This is exported as a promise, We can then pull the pool export into the app program and use the function.
```javascript
const db = require('location of database'/database)

db.execute('SELECT * FROM products')

```
You can then insert your SQL command into this field. Now there is a better way to do this using Sequelize. This is a package that esssentially removes a lot of the SQL code requirements and adds convienence methods around the application. You can install with

```javascript
npm install --save sequelize 
```
You can then create a new database file
```javascript
const Sequelize = require('sequelize');

const sequelize = new Sequelize('node-complete', 'root', 'Motorstorm1!', {
  dialect: 'mysql',
  host: 'localhost'
});

module.exports = sequelize;
```
Now in Sequelize we can define a model in a way that suits our data. We first add a path to our connection file and then use the keyword __define__
```javascript
const Sequelize = require('sequelize');

const sequelize = require('../util/database');

const Product = sequelize.define('product', {
  id: {
    type: Sequelize.INTEGER,
    autoIncrement: true,
    allowNull: false,
    primaryKey: true
  },
  title: Sequelize.STRING,
  price: {
    type: Sequelize.DOUBLE,
    allowNull: false
  },
  imageUrl: {
    type: Sequelize.STRING,
    allowNull: false
  },
  description: {
    type: Sequelize.STRING,
    allowNull: false
  }
});

module.exports = Product;

```
This is the equivalent of using SQL to create a new table. Once our model has been created we can then connect to our database using the __sync__ keyword
```javascript
const Sequelize = require('sequelize');

const sequelize = require('../util/database');

sequelize.sync().then(result => {
    console.log(result)
})
.catch(err => {
    console.log(err)
})
```
When you run this code and you don't have an error, you syould see the result. You will notice that sequelize is clever enough that if you have a new model without a corresponding table it will create one. However if you run this query and it has already created a table it will just connect to the database. This is done with the SQL command:
```SQL
CREATE TABLE IF NOT EXISTS 'products'
```
In some cases you may change some of your tables to better suit your project. If you want to avoid creating new tables and deleting the old ones you can use the command __{ force: true }__ in the sync function.

```javascript 
sequelize.sync( { force: true })
.then(result => {
    console.log(result)
})
.catch(err => {
    console.log(err)
})
```
To then upload data into our SQL database we can use the keyword __create__. In this specific example this would sit in the controllers of a Node.js application. 

```javascript
const Product = reqiure('location of your Sequielize Product model/product')


exports.postAddProduct = (req, res, next) => {
    const title = req.body.title;
    const price = req.body.price;
    const description = req.body.description;

    //Product 

    Product.create({
        title: title, 
        price: price,
        description: description
    }).then(result => console.log(result))
    .catch(err => console.log(err)))
}

```
The new product should then appear in your database with the values that were pulled from the POST request in the HTML code. Now let's say we want to pull all of the results in a SQL database. This can be achieved with the find all command.
```javascript
const Product = reqiure('location of your Sequielize Product model/product')

Product.findAll().then(products => console.log(products)).catch(err => console.log())
```
In order to retrieve a single product we use the keyword __findByPk__.

```javascript
Product.findByPk(prodID).then(product => console.log(product)).catch(err => console.log(err))
```

If you would like to search for a single product you can use this code. Worth noting that this will return an array so we want to have a [0] on our product to tell the program to return the first element of the array.

```javascript
Product.findByPk({where: {id: prodID } })
.then(product => console.log(product[0]))
.catch(err => console.log(err))
```

If we want to amend an exisiting product we can use the findByd and __save__ keyword. If the product doesn't exist it will create a new one, if it does exist it will then amend the existing line.

```javascript

updatedTitle = 'New Title'
updatedPrice = 2.99
updatedDesc = 'This is awesome!'

Product.findByPk(prodId).then(product => {

    product.title = updatedTitle
    product.price = updatedPrice
    product.description = updatedDesc
    return product.save()
}).then(result => console.log('Updated Product'))
.catch(err => console.log(err))
```
In this example we have a nested promise. The save function is also a promise as well as findByPk. Therefore we need to have then() statement after the first then() statement to catch the second promise. The catch() function will then catch the errors for both promises. Finally if we want to delete a product we need to use the keyword __destroy__.

```javascript   

Product.findByPk(prodId).then(product => {
    return product.destroy()
}).then(result => console.log('Destroyed Product'))
.catch(err => console.log(err))

```
## Associations ##
In sequelize there are a number of good inputs to help deploy things that would have been otherwise cumbersome. For example, if you would like to relationship map there are some commands that all you to do that. 

```javascript
const A = sequelize.define('A', /* ... */);
const B = sequelize.define('B', /* ... */);

A.hasOne(B); // A HasOne B - B has the primary key
A.belongsTo(B); // A BelongsTo B - - A has the primary key
A.hasMany(B); // A HasMany B - B has the primary key
A.belongsToMany(B, { through: 'C' }); // A BelongsToMany B through the junction table C

```
The order in which the association is defined is relevant, in this case A is the source model and B is the target model. Sequelize will automatically create a primary key for these tables. 

When we have created this relationship sequelize can do a really clever thing with the function __create()__. Lets say we have defined relationship with a varaible of belongs to.

```javascript

Product = //sequelize instace
User = //sequelize instace

Product.belongsto(User, { constraints: true, onDelete: 'CASCADE' })

```
The 'CASCADE' input tells sequelize when we delete the user the associated products also dissapear. Contraints says the Product table must use a foreign key from the Users table only. We can then declare a varaible in the request object with the id 1. You will notice that the product table will suddenly have user_id column to link it to the User table.

```javascript
app.use((req, res, next) => {
    User.findByPk(1).then(user => {
        req.user = user
        next()
    }).catch(err => console.log(err))
}

```

Worth noting that the req.user variable can be accessed by all middleware. This is quite a neet way to declare a global varaible across middleware. Finally we go to the function that saves products to out SQL tables. 

```javascript
exports.postAddProduct = (req, res, next) => {
  const title = req.body.title;
  const imageUrl = req.body.imageUrl;
  const price = req.body.price;
  const description = req.body.description;
  req.user
  .createProduct({
    title: title,
    price: price,
    imageUrl: imageUrl,
    description: description
  })
  .then(result => {
    // console.log(result);
    console.log('Created Product');
    res.redirect('/admin/products');
  })
  .catch(err => {
    console.log(err);
  });
};
```
The createProduct method will create a new product with a user_id values of 1. It is worth noting the Sequelize is clever enough to recognise the "Product" word after create and knows this is refering to the Product table in SQL. This could be creatCart(), createUser() etc. depending on what the relationship mapping is. This link has also been done automatically. Once we have our association we can use the sequelize function __getProducts()__. This will return a speficic product based on the user_id.


```javascript
req.user.getProducts({ where { id: 'Insert Id you are looking as an integer here'}}).then().catch()

```
This all works for a One to Many relationship, but what happens when we want to do a Many to Many relationship. We need to use the __through__ keyword. Take the example of a Users cart, a user has one cart which can hold many products. Additionally a product can be in multiple carts. To set up the correct tables in sequelize we do the following:

```javascript

Cart.belongsTo(User)
User.hasOne(Cart)
Cart.belongsToMany(Product { through: CartItem })
Product.belongsToMany(Cart { through: CartItem })

```
The CartItem table will link these two tables together and is an example of an __intermediate__ table.

