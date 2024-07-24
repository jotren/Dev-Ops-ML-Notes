# Advanced Authentication & Authorization #

Node.js is built for handling requests and responses. However, it is not setup for being a mail server. You typically use third party mail servers to handle millions of emails.

Third party providers and packages can help send mail via Node.js. It can be achieved using SendGrid, it a free service if you send less than 100 emails a day. You need to install two packages.

```javascript
npm install --save nodemailer nodemailer-sendgrid-transport
```

Youn need to create an account to be able to send emails, the link can be found [here](https://sendgrid.com/).

We then need to create an APi key on the website which can be achieved by clicking on settings > API keys > Create API key:

![images](images/sendgrid_example_1.png)


![images](images/sendgrid_example_2.png)


![images](images/sendgrid_example_3.png)

This process will create an API key which we then insert into our const transporter.

```javascript

const sendgridTransport = require('nodemailer-sendgrid-transport')

const transporter = nodemailer.createTransport(sendgridTransport({
    auth: {
            api_key: //insert API key as a string
    }
}))

```
Let's look at the example of sending an email once you have logged in. If you have an action with a then block you can then insert this code into the middleware function.

```javascript
 .then(result => {
          res.redirect('/login');           //
          return transporter.sendMail({
            to: email,
            from: 'shop@node-complete.com', 
            subject: 'Signup succeeded!',
            html: '<h1>You successfully signed up!</h1>'
          });
        })
        .catch(err => {
          console.log(err);
        });

```

# Resetting Passwords #

To start we need to create a new view, this view will simple be a bar with an option to submit an email. Still need to have the crsf tokens etc. We start with the get request

```javascript
exports.getReset = (req, res, next) => {
  let message = req.flash('error'); //This section picks up any errors with the inputs
  if (message.length > 0) {
    message = message[0];
  } else {
    message = null;
  }
  res.render('auth/reset', {         //Then we render our reset page
    path: '/reset',
    pageTitle: 'Reset Password',
    errorMessage: message
  });
};

```

Next we need to work on our post request. In order to do this we need to create a unique token so that the user can verify that users can only change the password when emailed a reset link. Node.js has a built in cryptography library that we can leverage

```javascript
const crypto = require('crypto')

```
This const is able to create unique and random values. 


```javascript
exports.postReset = (req, res, next) => {
  crypto.randomBytes(32, (err, buffer) => {   //Create 32 random bites, callback function will return a buffer of these bytes. 
    if (err) {
      console.log(err);
      return res.redirect('/reset');
    }
    const token = buffer.toString('hex');      //The token will then be generated from the hexidecimal values in randomBytes
    User.findOne({ email: req.body.email })        //We need to find the user for the account we are trying to reset. 
      .then(user => {
        if (!user) {                                                    //Then once we have the user can begin to change elements in the database
          req.flash('error', 'No account with that email found.'); 
          return res.redirect('/reset');
        }
        user.resetToken = token;                                  //In the user class we need to create a resetToken: String input that will be saved in the MongoDb database. 
        user.resetTokenExpiration = Date.now() + 3600000;           
        return user.save();                                       //We then commit the resetToken and resetTokenExpiration date to the user class and database, number is in ms
      })
      .then(result => {                                             //If this has then worked we can begin out send mail function using sendgrid. 
        res.redirect('/');
        transporter.sendMail({
          to: req.body.email,
          from: 'shop@node-complete.com',
          subject: 'Password reset',
          html: `
            <p>You requested a password reset</p>
            <p>Click this <a href="http://localhost:3000/reset/${token}">link</a> to set a new password.</p>  
          `      //We then put the token in the URL to be manipulated later
        });
      })
      .catch(err => {
        console.log(err);
      });
  });
};

```

Now we need to create a page that will be generated when the user clicks the link. This should only have an update password field.

```javascript
exports.getNewPassword = (req, res, next) => {
  const token = req.params.token;                                                           //We can fetch the token from the request object. 
  User.findOne({ resetToken: token, resetTokenExpiration: { $gt: Date.now() } })            //this function will find the user that we provided the token to in the URL. 
    .then(user => {
      let message = req.flash('error');
      if (message.length > 0) {
        message = message[0];
      } else {
        message = null;
      }
      res.render('auth/new-password', {               //If the user can be verified the page will be rendered. 
        path: '/new-password',
        pageTitle: 'New Password',
        errorMessage: message,
        userId: user._id.toString(),
        passwordToken: token                        //Then we need to pass the token into the new page. This will be a hidden value in html, but we then will grab later in a post request
      });
    })
    .catch(err => {
      console.log(err);
    });
};
```

In the controllers we need to then include the :tokens so that it can be pulled from the req. object. 

```javascript
router.get('/reset', authController.getReset);

router.post('/reset', authController.postReset);

router.get('/reset/:token', authController.getNewPassword);
```
This means that the req.params.token will return the token value shared in the URL. The req.params property is an object containing properties mapped to the named route “parameters”. For example, if you have the route /student/:id, then the “id” property is available as req.params.id. This object defaults to {}.

So now we need to add to our contoller a post request to send the new password to the database

```javascript
router.post('/new-password', authController.postNewPassword);
```
Then we create the postNewPassword function that will extract the new password from the req.body.password 

```javascript

exports.postNewPassword = (req, res, next) => {
  const newPassword = req.body.password;                //We grab the name, userID and passwordToken from the body. The passwordToken was inserted in the previous get request when the page was rendered.
  const userId = req.body.userId;
  const passwordToken = req.body.passwordToken;
  let resetUser;

  User.findOne({                                        //Compare this to a user in the MongoDB database
    resetToken: passwordToken,
    resetTokenExpiration: { $gt: Date.now() },
    _id: userId
  })
    .then(user => {
      resetUser = user;                                //Then save the new user and provide a new bcyrpt encrypted password. 
      return bcrypt.hash(newPassword, 12);
    })
    .then(hashedPassword => {                           //Once we encrypt the new password we return a promise that will reset all the new users information and return the token info to undefined.
      resetUser.password = hashedPassword;
      resetUser.resetToken = undefined;
      resetUser.resetTokenExpiration = undefined;
      return resetUser.save();                          //Push this new info into the MongoDB database 
    })
    .then(result => {
      res.redirect('/login');
    })
    .catch(err => {
      console.log(err);
    });
};
```

