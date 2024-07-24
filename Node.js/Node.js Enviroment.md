# Node.js Enviroment and Packages #

This program is essential for running node.js applications. To set up in the folder in which your app is running you type:

```javascript
npm init
```
This will prompt a number of inputs such as author, license, descrption etc. You can fill this in but it is not essential. One this is complete a package.json will appear looking like this:
```javascript
{
  "name": "express.js_practise",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
  },
  "author": "",
  "license": "ISC"
}
```
In order to create a shortcut to start your program it is common practise to insert "start": "node app.js" into this file to instruct the program how to intialise the app. 
```javascript
{
  "name": "express.js_practise",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
  },
  "author": "",
  "license": "ISC"
  "start": "node app.js"
}
```
Nodemon is a 3rd party package that will re-run the server if there are any changes to the file. We simply make the change and save the file to re-run the app.
To start this you type:

```javascript
npm install nodemon --save-dev
```
The -dev command tells the computer this package is only being used an development rather than in production. This will be installed into this project. If you then change the package.json file again to say   "start": "nodemon app.js", the application will restart any time you save the files.
```javascript
{
  "name": "express.js_practise",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "nodemon app.js"
  },
  "author": "",
  "license": "ISC"
}
```
To install express you type the command. The reason we install locally instead of globally is so that the applications are easy to transfer onto servers.
```javascript
npm install --save express
```
Once this is complete you will see the dependancies location in the package.json file update.

Another package we will commonly use is EJS. To install this in your local Node.js file
```javascript
npm install --save ejs
```