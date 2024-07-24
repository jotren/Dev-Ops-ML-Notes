# Node.js NoSQL #

The database we are going to use is called MongoDB. This is a NoSQL database which means there are no schemas (which essentially mean constrained structures like table headers, joins etc.). NoSQL databases can scale much faster than SQL because it is able to scale horizontally. This means that it is easy to add multiple servers to manage the data. This is difficult in SQL because once you have data on multiple servers the joins are tricky. 

In MongoDB the data looks similar to a javascript class or python dictionary. 

```javascript
{
  "_id": 1,
  "name": "Jonah Trenouth",
  "addresses": '93G Denmark HIll'
}
```

There are two ways to store complex data in a NoSQL database. You can either embed the data or reference it. Embed is when you have parents classes like so:

```javascript
{
  "_id": 1,
  "name": "Ashley Peacock",
  "addresses": [
    {
      "address_line_1": "10 Downing Street",
      "address_line_2": "Westminster",
      "city": "London",
      "postal_code": "SW1A 2AA"
    }
}
```

Reference is when the data is stored in a seperate collection to the parent. 

```javascript
{
  "_id": 1,
  "name": "Ashley Peacock",
  "addresses": [
    1000,
    1001
  ]
}
// Stored in the address collection
{
  "_id": 1000,
  "address_line_1": "10 Downing Street",
  "address_line_2": "Westminster",
  "city": "London",
  "postal_code": "SW1A 2AA"
}
// Stored in the address collection
{
  "_id": 1001,
  "address_line_1": "221B Baker Street",
  "address_line_2": "Marylebone",
  "city": "London",
  "postal_code": "NW1 6XE"
}
```

[Here is an excellent link](https://betterprogramming.pub/embedded-vs-referenced-documents-in-mongodb-how-to-choose-correctly-for-increased-performance-d267769b8671) showing the benefits of both systems. 

## Connecting a Database ##

Once you create an account and sign up for a free cluster on the [https://cloud.mongodb.com/v2#/org/639061a0804e92276ec698a1/projects](website) you can connect to the database by clicking connect and selecting the option to connect through the app.

![image](images/MongoDB_database.png)

Once you click connect there will be an option to copy a URL. This URL will allow you t

 connect to your cluster.
```
mongodb+srv://<username>:<password>@cluster0.8xxlss2.mongodb.net/?retryWrites=true&w=majority
```

This URL has the inputs username and password. You want to create a usernam and password that is seperate to the admin role. Under security you then click on Database Access and Add New Database User. You create a username and password that will then be inserted into the connect constant. The connect code is as follows

```javascript
const mongodb = require('mongodb')
const MongoClient = mongodb.MongoClient

let _db; //underscore highlights only used in this block

const mongoConnect = () => {
  MongoClient.connect('mongodb+srv://node_mongo_access:oVowTTftzcufyT4t@cluster0.8xxlss2.mongodb.net/shop?retryWrites=true&w=majority') //shop is the name of the database
.then(client => {
  console.log('Connected')
  _db = client.db()
})
.catch(err => {
  console.log(err)
  throw err
})
}

const getDb = () => {
  if (_db) {
    return _db;
  }
  throw 'No database found'
}

exports.mongoConnect = mongoConnect
exports.getDb = getDb
```
## MongoDb CRUD Operations ##

mongoConnect will connect to the database and the getDb constant will be used to send data to the database. MongoDb will automatically create a database when called. Before we define the connect promise we create a variable _db. Within the mongoConnect promise we will asign this _db varaible with access to the database. Once we have created this constant, it can be imported in other files to connect to the database. For example if we want to connect ot the database in a class we use the __collection()__ and __insertOne()__ keywords.

```javascript
const getDb = require('../util/database').getDb;

class Product {
  constructor(title, price, description, imageUrl) {
    this.title = title;
    this.price = price;
    this.description = description;
    this.imageUrl = imageUrl;
  }

  save() {
    const db = getDb();
    return db
      .collection('products')
      .insertOne(this)
  }
```
The colletion keyword defines the table name, which is the example above will now be shop.products (shop is defined in the MDB URL). Then when we call the function on the class product the constructor will be fed into the database using the insertOne() keyword. In order to connect to MongoDb in the application we feed this code into the app.js file

```javascript

const mongoConnect = require('./util/database').mongoConnect

mongoConnect((client) => {
  console.log(client)
})
```
Now MongoDb doesn't use seuqlize as it does not require a relational database. Instead we need to treat this database as individual class objects. If we wanted to fetch all or a single product we would need to use a static function on the Product class model. We then need to use the keywords __collection()__, __find()__, and __toArray()__.


```javascript
static fetchAll() {
  const db = getDb();
  return db
    .collection('products')
    .find()
    .toArray()
}
```

The find function will return a cursor, which consists of lines of code. The to array function will convert this cursor into a javascript array. To call this function you would then use the Product.fetchAll() static function. In ordert to return a single product we use a similar method.

```javascript
const mongodb = require('mongodb')

static findById(prodId) {
  const db = getDb();
  return db
    .collection('products')
    .find({ _id: new mongodb.ObjectId(prodId) })
    .next()
}
```
However in this case we use the __next()__ keyword. This keyword will return the next document in the cursor. As we have only called one, this will return the ID of the product we are looking for. We need to use the built in mongodb.ObjectId input because you cannot compare an MDB id object with a string. Here are a few more MongoDb functions

```javascript

db.collection('products').updateOne(this) //This will update a single line

```
Worth noting the ID will remain the same but the other fields will be updated. If you wanted to declare a global MongoDb object variable we can use the following code.

```javascript
const mongodb = require('mongodb')
const ObjectId = mongodb.ObjectId

new ObjectId(prodId)
```

Another thing to note is that if you want to update a database item use __updateOne()__ the data within a database you can simply. To combine this all together you could use an if statement to update an item if it already exists and insert an item if it is new. e.g. You need to designate dpOp so that you can return something to the promise at the end of the if statement.

```javascript

class Product { 
  constructor(title, price, description, imageUrl, id) {
    this.title = title;
    this.price = price;
    this.description = description;
    this.imageUrl = imageUrl;
    this._id = id ? new mongodb.ObjectId(id): null //Need to use a ternary expression incase no id is passed. Notice how we convert the id here rather than later in the code. Reduce keyboard strokes
  }


  save() {
    const db = getDb();
    let dbOp;
    if (this._id) {
      //Update the product
      dbOp = db
        .collection('products')
        .updateOne({ _id: this._id }, { $set: this });
    } else {
      //Create a new product
      dbOp = db.collection('products').insertOne(this);
    }
    return dbOp
  }
  ```

Finally if we would like to delete an item we can use the __deleteOne()__ MongoDb function. Assume we still reside in the same class.

```javascript
statis deleteById(prodId) {
  
  db.collection('products').deleteOne({ _id: new mongodb.ObjectId(prodId) }) //Notice you have to redeclare the prodId as it is a method seperate from the constructor
}

```

To review here are the important commands

```javascript
find() //Return a single item or number of cursors
toArray() //Convert the cursors into a number of 
insertOne() //Insert a sinlge document into the database
updateOne() //Update an exisiting item in the database -> Needs to be a MongoDb Id Object to work
deleteOne() //Remove a document from the database

```
## Mongoose ## 

Mongoose is essentially the sequelize of MongoDb. In order to connect to the MongoDb database we use the code

```javascript
const mongoose = require('mongoose');

mongoose
  .connect(
    'mongodb+srv://node_mongo_access:oVowTTftzcufyT4t@cluster0.8xxlss2.mongodb.net/?retryWrites=true&w=majority'
  )
  .then(result => {
    app.listen(3000);
  })
  .catch();

```

First we need to create a data schema. This is done by feeding Mongoose with a javascript class object. Now this is interesting because the whole point about MongoDb is that it is schemaless. However, in our applications most data structures are regular and mongoose attemps to provide us with that structure.

```javascript
const mongoose = require('mongoose')

const Schema = mongoose.Schema

const productSchema = new Schema({
  title: {
    type: String,
    required: true //This forces every product in your database to have a title, similar to no nulls in SQL
  }
  price: {
    type: Number,
    required: true
  }

})
```

Once you have defined your schema you then export a model to the rest of the application
```javascript
module.exports = mongoose.model('Product', productSchema) //Mongoose will take this 'Product' name and create the products collection automatically
```

Then when you want to upload values into the database you treat the mongoose model like a class

```javascript

const Product = require('location of Mongoose Model')

const title = req.body.title
const price = req.body.price
const product = new Product(title: title, price: price)

product.save().then().catch() //In MongoDb this save() function was defined in the Product model, however this is now a built in mongoose function

```
In order to pull all of your products in your database you need to use the keyword __find__()

```javascript
Product.find().then(products => console.log(products)).catch(err => console.log)

```
In order to return a single product the keyword is the same but we need to pass the __findById()__ function a unique ID (again uniquye to Mongoose).

```javascript
Product.findById(prodId).then(product => console.log(product)).catch(err => console.log)

```
To edit a product you need to use the __findById()__ and __save()__ function. Don't forget you need return at the end of the then statement. 

```javascript
Product.findById(prodId).then(product => {
  product.title = updatedTitle
  product.price = updatedPrice
  return product.save().then().catch()
}

```
Finally if you want to delete a product you use the __findByIdAndRemove()__ function and __remove()__ function.

```javascript
Product.findByIdAndRemove().then().catch()
```
Worth noting that in some cases we create a mongoose input that required a ObjectId. This is the structure of the code.

```javascript

const userSchema = new Schema({

  cart: {
    items: [{ productId; {type: Schema.Types.ObjectId, required: true}, //This example shows the instances when you need to create a unique MongoDb Id. This value will be automatically created on the creation of a new product
    quantity: { type: Number, required: true}
    }]
  }
})
  ```
Mongoose is very clever as it allows us to join different models together unlike MongoDb. The way this is done is in the schema create at the start of the process.

```javascript
const mongoose = require('mongoose');

const Schema = mongoose.Schema;

const productSchema = new Schema({

  userId: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true
  }
});

///Seperate model

const userSchema = new Schema({
  cart: {
    items: [
      {
        productId: {
          type: Schema.Types.ObjectId,
          ref: 'Product',
          required: true
        },
        quantity: { type: Number, required: true }
      }
    ]
  }
});

```

The two models are linked using a key word __ref__. If we would like to see a number of products with the same userID you can use the keyword __populate()__. 
Here is an excellent [Stack Overflow](https://stackoverflow.com/questions/38051977/what-does-populate-in-mongoose-mean) about populate which will help understanding. Essentially what populate does is look up nested IDs and return items in that ID.