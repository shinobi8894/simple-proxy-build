# Simple Proxy Build using Node.js

In a few easy steps we are going to create a simple proxy in Node.js which can forward requests to multiple different servers/endpoints!

## Initialize the project
Let’s start by initiating a new node project:
```
npm init
```
This will generate a package.json file which will contain a basic project configuration. The command will prompt you with multiple questions (name, version, description, etc.) - you can click ‘Enter’ on all of them to accept the default values (for example, by default the entry point will be index.js).

## Install dependencies

We need a few packages to make the proxy work:

```
express: Minimalist web framework
http-proxy-middleware: Simple proxy framework
(optional) morgan - HTTP request logger middleware
```
which we can install by running:

```
npm i express http-proxy-middleware morgan
```
## Define a start command

We need to add a start command to our project, so after a small change, your package.json file should look like this (Depending on when you install these packages, some version numbers might differ, but their core functionality should stay the same. If the behaviour is drastically different, check their latest documentations.):

```
{
 "name": "simple-nodejs-proxy",
 "version": "1.0.0",
 "main": "index.js",
 "license": "MIT",
 "dependencies": {
   "express": "^4.17.1",
   "http-proxy-middleware": "^1.0.5",
   "morgan": "^1.10.0"
 },
 "scripts": {
   "start": "node index.js"
 }
}
```
If we now add an empty file index.js, we can execute the project through:

```
yarn start
```
This should run the file. Because the file is empty the console output should also be empty.
But enough configurations, let’s actually proxy some requests!

## Create a simple Proxy

This is where the interesting bits begin. Go ahead and open the index.js file and add the necessary imports:

```
const express = require('express');
const morgan = require("morgan");
const { createProxyMiddleware } = require('http-proxy-middleware');
```

```
// Create Express Server
const app = express();

// Configuration
const PORT = 3000;
const HOST = "localhost";
const API_SERVICE_URL = "https://jsonplaceholder.typicode.com";
```

```
// Logging
app.use(morgan('dev'));
```

```
// Info GET endpoint
app.get('/info', (req, res, next) => {
   res.send('This is a proxy service which proxies to Billing and Account APIs.');
});
```

```
// Authorization
app.use('', (req, res, next) => {
   if (req.headers.authorization) {
       next();
   } else {
       res.sendStatus(403);
   }
});
```
```
// Proxy endpoints
app.use('/json_placeholder', createProxyMiddleware({
   target: API_SERVICE_URL,
   changeOrigin: true,
   pathRewrite: {
       [`^/json_placeholder`]: '',
   },
}));
```
```
// Start the Proxy
app.listen(PORT, HOST, () => {
   console.log(`Starting Proxy at ${HOST}:${PORT}`);
});
```
