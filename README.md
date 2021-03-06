# hot-mock-middleware

A express middleware that creates mock server. Your can specify a directory, the files under the directory are mock files that export interface definitions，support for real-time refresh based on 'require' dynamic analysis.

## Installation
```sh
$ npm install --save-dev hot-mock-middleware
```

## Quick start

1. Create a directory `mock-server`, then create the file `app.js`, the content is：
```js
const express = require('express');
const path = require('path');
const mockMiddleware = require('hot-mock-middleware');

const app = express();
// Use middleware to create mock server
app.use(
    mockMiddleware(path.resolve(__dirname, 'mock'))
);

app.listen(3000, () => console.log('Example app listening on port 3000!'));
```

2. Create a `mock` directory under `mock-server` and create `users.js` as follows:
```js
// mock/users.js

module.exports = {
  // supported values are Object and Array
  'GET /api/users': { users: [1, 2] },

  // GET can be omitted
  '/api/users/1': { id: 1 },

  // support for custom functions, APIs refer to express@4
  'POST /api/users/create': (req, res) => { res.end('OK'); },
};
```

3. Install dependent modules and start app:
```sh
$ npm i -S express hot-mock-middleware
$ node app.js
```

The you can then access the <http://localhost:3000/api/users> to view API documents.   
You can create more js files in the `mock` directory, the mock server will automatically grab contents, also you can modify these files, ths api data will refresh real-time without restarting app.

## Using with Webpack
To use with webpack projects, simply add a setup options to your webpack-dev-server options.  
Change your config file `webpack.config.js`: 
```js
const path = require('path');
const mockMiddleware = require('hot-mock-middleware');

module.exports = {
    // ...

    devServer: {
        before(app) {
            app.use(
                mockMiddleware(path.resolve(__dirname, 'mock'))
            );
        }
    }

    // ...
}
```
Let's add a script to easily run the dev server as well: `package.json`: 
```json
{
    "scripts": {
        "start": "webpack-dev-server --open",
        "build": "webpack"
    }
}
```
Then you can add some mock files in `mock/` directory.

## Using with create-react-app
To use with projects that created with create-react-app, create `src/setupProxy.js` as follows: 
```js
// src/setupProxy.js
const path = require('path');
const mockMiddleware = require('hot-mock-middleware');

module.exports = app => {
    app.use(mockMiddleware(
        path.resolve(__dirname, '../mock')
    ));
}
```
Then you can add some mock files in `mock/` directory.

## Demo
- [Using with create-react-app](https://github.com/vanni-li/hot-mock-middleware/tree/master/examples/create-react-app)
- [Using with express](https://github.com/vanni-li/hot-mock-middleware/tree/master/examples/express)
- [Using with webpack](https://github.com/vanni-li/hot-mock-middleware/tree/master/examples/webpack-cli)
- [Using with webpack-dev-server Node.js API](https://github.com/vanni-li/hot-mock-middleware/tree/master/examples/webpack-api)
- [Using with express + webpack-dev-middleware](https://github.com/vanni-li/hot-mock-middleware/tree/master/examples/webpack-middleware)
