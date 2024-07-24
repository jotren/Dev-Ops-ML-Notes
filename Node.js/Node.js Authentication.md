# Authentication #

Below is the structure of authentication. When someone logs in we use the session middleware to sotre info of user authentication. Then the server will return a cookie in the client side that has a session ID, meaning they don't need to continually log into the website. 

![images](images/authentication.png)

In order to start the authentication program we need a few things in place first. The model needs to be MongoDB, and we need to create an exitiing User class that has a construtor save() function to push all the user details to MongoDB.

```javascript
exports.postSignup = (req, res, next) => {
  const email = req.body.email; 
  const password = req.body.password;
  const confirmPassword = req.body.confirmPassword;
  User.findOne({ email: email })
    .then(userDoc => {
      if (userDoc) {
        return res.redirect('/signup');
      }
      const user = new User({
        email: email,
        password: password,
        cart: { items: [] }
      });
      return user.save();
    })
    .then(result => {
      res.redirect('/login');
    })
    .catch(err => {
      console.log(err);
    });
};
```

This middleware has a few steps:

1) We first take the email and password from the request block

2) We use the MongoDB function findOne() to identify if a user already exists

3) An if function will define if a user is returned and redirected

4) If not we create the new User and save it to MongoDB using the built in save() function

5) Once we have saved the result we redirect to the login page to utilise the new users credentials

The issue with this method as it stands is that the password is not encrypted, making it easy to see sentitive information. If the database is comprimised, the user passwords are exposed. What we should do is encrypt the password. So that even if you have the database leaked they can only get the email. To install the encryption package install:

```javascript
npm install --save bcryptjs
```

The code of bcrypt is the following, we can amend our exisiting postSignup middleware export. The first argument of the bcrypt function is the string you would like to encrypt. The second is the salt number. 

```javascript

const bcrypt = require('bcrypt')

exports.postSignup = (req, res, next) => {
  const email = req.body.email;
  const password = req.body.password;
  const confirmPassword = req.body.confirmPassword;
  User.findOne({ email: email })
    .then(userDoc => {
      if (userDoc) {
        return res.redirect('/signup');
      }
      return bcrypt
        .hash(password, 12)
        .then(hashedPassword => {   //So we need to create another then statement and place our new hashedPassword object into the user class.
        const user = new User({
            email: email,
            password: hashedPassword, 
            cart: { items: [] }
        });
      return user.save();
    })
    .then(result => {
      res.redirect('/login');; //Now the bcrypt keyword will actually return a promise
    })
    
    })
    .catch(err => {
      console.log(err);
    });
};

```

When we are then logging in, we can check whether two passwords are the same using the __compare()__ keyword. Now with encryption it is impossible to know whether the password will exactly match. But what the compare keyword does is uses an algorithmn to check whether it is POSSIBLE for the string to be convereted into the hashedPassword. Essentially, bcrypt coudl encrypt in millions of possible ways, we just need to check that one of those million possibilities is correct. The __compare__ keyword will return a boolean. 

If we do have a matching password we then want to create a session