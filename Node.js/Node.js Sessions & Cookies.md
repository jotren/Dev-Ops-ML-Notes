# Sessions and Cookies #

One interesting thing to note about requests and responses. As soon as you have called a res.render object, any req.value objects will be lost. For example:

```javascript
app.get((req, res, next) => {
  req.isAuthenticated = true
  res.redirect('/')
})
```

The req object cannot be called as the res.redirect function has been activated. The redirection creates a brand new request. The way to get around this is to use a global variable, however, if this was global it would also mean it would be accesible to all users. This is where cookies can help. A cookie can store data from the user that will be stored in the browser.

```javascript
app.get((req, res, next) => {
  res.setHeader('Set-Cookie', 'loggedIn=true')
  res.redirect('/')
})
```
Then if you go to your browser in localhost, inspect and click on the application tab you will see you cookie with the value true in the cookies section.

![image](images/cookies.png)

In order to then pull your cookie into the application you can use the command within your middleware. The following code should work in a simple application.

```javascript
app.get('/cookie', (req, res, next) => {
  res.setHeader('Set-Cookie', 'loggedIn=true')
  res.redirect('/get-cookie')
})

app.get('/get-cookie', (req, res, next) => {
  const cookieValue = req.get('Cookie').split('=')[1]
  console.log(cookieValue)
  res.redirect('/')
})

```
Because you can see what cookies are saved onto a browser you must not store sensitive information or keys in this field. With cookies you can add multiple cookies and key value pairs. You can also have multiple data points in the same cookie.

```javascript
Max-Age=10 //This would be the max amount of time the cookie is availble (seconds). Example of this is timing out in banking software
Secure //This means the cookie will only be sent via https
HttpOnly //Can't access the cookie through client side server. Protects you from cross site scripting. This is important for authentication
```
## Sessions ##

In order to start a session you need to download the package with the command. Part of the suite but not automated in express OG package.

```javascript
npm install --save express-session

```

A session is a construct that allows you to store information in the backend that isn't the request function. This will only store information for the same user. Cookies are browser side and sessions are server side. 

```javascript
const session = require('express-session');

app.use(
  session({ secret: 'my secret', resave: false, saveUninitialized: false })
);
```

Secret is a random unique key string used to authenticate a session. Resave tells the program no .... SaveUnitialized also.... Cookie is where you configure the cookie if you don't want to use default. Now that we have added the session middleware in the app. We can reference in other files. 

```javascript
exports.postLogin = (res, req, next_) => {
  req.session.isLoggedIn = true
  res.redirect('/')
}
```

This will create a new cookie called connect(+something), which is a session cookie that will identify the user and instance of website to the server. It will be destroyed when you leave the browser and start again. In order to visualise your session you can use the following code in other middleware.

```javascript

console.log(req.session.isLoggedIn)

```
This isLoggedIn object will be accessible from different middleware, but not different users. This is the core mechanism behind authentication. We can save session information by installing the mongoDB session package

```javascript
npm install --svae connect-mongodb-session
```
This will let the session package store data in the mongodb database. To set up the store:

```javascript
MongoDBStore = require('connect-mongodb-session')(session)

const MONGODB_URI = 'mongodb+srv://maximilian:9u4biljMQc4jjqbe@cluster0-ntrwp.mongodb.net/shop'

const store = new MongoDBStore({
  uri: MONGODB_URI,
  collection: 'sessions'
})

```
For some reason, we need to remove '?retryWrites=true' from the end of the URL. This isn't explained at all in the tutorials, but you can find documentation on retry writes [here](https://www.mongodb.com/docs/manual/core/retryable-writes/#:~:text=MongoDB%20retryable%20writes%20make%20only,but%20not%20persistent%20network%20errors.).


Now we can use the store as a session store and connect using the session create in the application.

```javascript
app.use(
  session({
    secret: 'my secret',
    resave: false, 
    saveUninitialized: false,
    store:store 
  })
);
```
 
 If we then want to start storing variables in the session database we can use the request object and session keyword. 

 
```javascript

req.session.isLoggedIn = true;
req.session.user = user;
return req.session.save(err => {
  console.log(err);
  res.redirect('/');
});

```
You save certain new items to the request object and then when you return __req.session.save()__ this will commit that data to the MongoDB database. It is worth noting that the middleware will automatically create the session ID, and store in MongoDB.  A new database would be created with the given name sessions. It is important to understand that cookies are client side and sessions are server side. 

Then when we want to end a session because someone has logged out of the account we want to use the __destroy()__ keyword.

```javascript
exports.postLogout = (req, res, next) => {
  req.session.destroy(err => {
    console.log(err);
    res.redirect('/');
  });
};
```
Here is quite a good (link)[https://meghagarwal.medium.com/storing-sessions-with-connect-mongo-in-mongodb-64d74e3bbd9c] on sessions that provides a concise overview and example code.

Now in order to make sure unregistered users cannot access information wihtout a login we need to protect our routing. This can be achieved by checking the sessions object we created called __isLoggedIn__ and having a conditional statement at the start of our middleware function.

```javascript
exports.getAddProduct = (req, res, next) => {
  if (!req.sessiom.isLoggedIn) {
    res.redirect('/login')
  } 
};
```

This will automatically redirect to a different section of the website if the session object isLoggedIn isn't defined. This can't happen unless the user logins in with a registered password and email. However this is not a very scalable way to protect routes as you would need to add this to every function. Instead what we can do is create a new middleware function

```javascript
module.exports = (res, req, next) => {
  if (!req.session.isLoggedIn) {
    res.redirect('/login')
  }
  next()
}
```

This middleware can then be exported and added to the routes of your application. The Node.js middleware function will always execute functions from left to right, so this function can be inserted to the LHS of the funtion.

```javascript
const isAuth = require('../middleware/isAuth') //Location of file

router.get('/add-product', isAuth, adminController.getAddProducts)

```

This is a much more scalable way to protect routes than with a conditional statement in each function.

## CSRF Protection ##

Which stands for cross-site request forgery is a type of computer hacking that we need to be aware of. Essentially it is when a hacker will create a dummy brower and send get requests to our server. We need to be able to make sure the server can identify requests sent from the real website vs. fake websites. This is achieved using a package called __csurf__

```javascript

npm install --save csurf

```

This package will send a randomised csurf token to the browser and check if the returned request has this token. This is quite a good (link)[https://www.youtube.com/watch?v=eWEgUcHPle0&t=268s] that explains the concept, the csurf token is known as an anti CRSF token. 

In order to implement CSRF protection in your application you type the following code.

```javascript

const crsf = require('csurf')

const crsfProtection = crsf()

app.use(crsfProtection)

```
In order for this to work you need to create a new middleware function that comes before the routes but after the user identification. In there we use a special res.locals field. This will deploy code in anything that is sent in res.render(). 

```javascript

app.use((res,req,next) => {
  res.locals.isAuthenticated = req.session.isLoggedIn;
  res.locals.csrfToken = req.crsfToken()
  next()
})

```

With this function now complete you need to add some code to any POST requests in HTML in order for the token to be created:

```
<form action="/logout" method="post">
    <input type="hidden" name="_csrf" value="<%= csrfToken %>">
    <button type="submit">Logout</button>

```
The important element in this HTML code is the input. This needs to be added to every aspect of your EJS files, there is not quick way to bypass.

If you ever need to create an error message in your page that pops up when the login has failed you can do this with the connect-flash package.

## Login Error Message ##

```javascript
npm install --save connect-flash

```
The issue is that as soon as you res.redirect your request object is nulled. So in the current format it isn't possible to create an error message that sticks around. To fix this we need to use the connect-flash package. We could do this with a session, but we want to add it to the session and remove it as the condition is met.

We then initialise in the application file.

```javascript
const flash = require('connect-flash')
```

And then in a section after you have initialised the session but before the routes you call the flash as a function. 

```javascript
app.use(flash())
```
Now we can use the flash function on any request object.

```javascript
exports.postLogin = (req, res, next) => {
  const email = req.body.email;
  const password = req.body.password;
  User.findOne({ email: email })
    .then(user => {
      if (!user) {
        req.flash('error', 'Invalid email or password.'); //This takes two arguments, the key under which the message will be named and the message itself.
        return res.redirect('/login');
      }
```
This req.flash() object occurs before the res.redirect(). Then we head to our get request that creates renders the login page.

```javascript

exports.getLogin = (req, res, next) => {
  let message = req.flash('error'); 
  if (message.length > 0) { //This will only trigger if we have an error set in flash i.e. passes the conditional statement.
    message = message[0];
  } else {
    message = null;
  }
  res.render('auth/login', {
    path: '/login',
    pageTitle: 'Login',
    errorMessage: message 
  });
};    
```
Here we push the req.flash error message into the express.js file. We then wrap this object in an EJS conditional statement. 

```html
<% if (errorMessage) { %>
    <div><%= errorMessage %></div>
<% } %>
```

If there is an error in te postLogin phase the it will be pushed the to front end. If you want to style this a little more you can use the following HTML and css code.

```html
<% if (errorMessage) { %>
    <div class="user-message user-message--error"><%= errorMessage %></div>
<% } %>
```

```css
.user-message {
  margin: auto;
  width: 90%;
  border: 1px solid #4771fa;
  padding: 0.5rem;
  border-radius: 3px;
  background: #b9c9ff;
  text-align: center;
}

.user-message--error {
  border-color: red;
  background: rgb(255, 176, 176);
  color: red;
}
```

