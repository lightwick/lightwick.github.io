---
layout: post
title: Setting Up Nodejs (with Express)
---

# Installing Express
```
npx express-generator --view="pug"
```
As of now, the default template engine for `express-generator` is *Jade*, which is the past version of *Pug*.

# Development Dependencies
We will install nodemon, a tool that automatically restarts the Nodejs application when there are file changes.
```
npm install nodemon --save-dev
```

# Custom Commands (nodemon)
Within the *package.json* file, inside the script element, add the following.
```
"scripts": {
    "start": "npm ./bin/www",
    "startserver": "DEBUG=APPLICATION_NAME:* nodemon ./bin/www -e js,pug,css"
  }
```
Change the *APPLICATION_NAME* with the actual name of the application.

By default, *nodemon* only restarts application when there are changes made to js files.\
The `-e js,pug,css` part makes it so that changes in other file extensions can also trigger a restart.