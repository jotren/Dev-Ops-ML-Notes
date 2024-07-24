### Node.js Express JS ###

You create an instance of an application using the commamd
```javascript
const express = require('express')
const app = express()
```
There are a number of express commands you need at your fingertips:

This command intialises the application on a port number (3000)
```javascript
app.listen(3000)
```
These functions are all HTML verbs.
```javascript
app.get() //--> get information (data/functions) from other routes or server. you CANNOT access info from the req.body
app.post() //--> post information (data) for example into SQL. you CAN access info from the req.body
app.put() //--> update specific information 
app.delete() //--> delete specific information
app.all() //--> covers all responses get, post etc.
```
We then have the middleware function
```javascript
app.use() //--> middleware call
```
When we are creating a new app we have to use a range of files and storage because it would be too complex and dense to run on a single script.

- Routes - This directs the requests on different URLS to the controllers
- Controllers - These hold the arrow functions in the website which are then executed (functionality and serve up HTML code)
- Views - This folder has all the HTML code that is served by the controllers
- Util - Holds the database connection information
- Public - Contains the CSS files that are then served up statically to the front end
- Models - If you have an objects that are stored/manipulated on the website the objects are stored here


Here is an example of a Express Js post and get request. 

GET Request: This request sends information from the server.


```javascript
app.get('/add', (req, res, next) => {
  res.send('<form action="/post " method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form#>')
})
```

POST Request: This request pulls information onto the server

```javascript
app.post('/post', (req, res, next) => {
  console.log(req.body)
  db.execute('SELECT * FROM products')
  .then(result => console.log(result))
  .catch(err => console.log(err))
  res.redirect('/add')
})
```

