# 00 Boilerplate

In this sample we are going to setup the basic plumbing to "build" our project and launch it in a dev server.

We won't install anything related about React, just some basic plumbing. In sample 01 we will start by importing
React and ReactDOM.

We will setup an initial <abbr title="Node.js package manager, a package manager for the JavaScript runtime environment Node.js">npm</abbr> project, give support to TypeScript, and install React.<br />
Then we will create a **helloworld.ts** sample.

Summary steps:

- Prerequisites: Install Node.js
- Initialize **package.json** (with `npm init`)
- Install:
    - Webpack and webpack-dev-server.
    - TypeScript.
    - Babel.
    - Bootstrap.
- Setup **webpack.config.js**
- Create a test js file.
- Create a simple HTML file.

# Prerequisites

Install [Node.js and npm](https://nodejs.org/en/) (v8.9.1) if they are not already installed on your computer.

> Verify that you are running at least node v8.x.x and npm 5.x.x by running `node -v` and `npm -v` in a terminal/console window. Older versions may produce errors.

## Steps to build it

- Create and navigate to the folder where you are going to create the empty project.

- Execute `npm init`, you will be prompted to answer some information request
about the project (e.g. set name to _samplereact_ and description to _Sample working with React,TypeScript and Webpack_).
Once you have successfully fullfilled them a **package.json** file we will generated.

 ```
 npm init
 ```

- Install **webpack** as a development dependency.

 ```
 npm install webpack --save-dev
 ```
- Install **webpack-dev-server** locally, as a development dependency (the reason to install it locally and not globally is to be easy to setup, e.g. can be launched on a clean machine without having to install anything globally but nodejs).

 ```
 npm install webpack-dev-server --save-dev
 ```

- Let's install a list of plugins and loaders that will add powers to
our webpack configuration (handling <abbr title="Cascading Style Sheets">CSS</abbr>, TypeScript...).

 ```
 npm install css-loader style-loader file-loader url-loader html-webpack-plugin awesome-typescript-loader extract-text-webpack-plugin --save-dev
 ```
- Let's add two commands to our **package.json** to build and start.

```diff
  "scripts": {
+    "start": "webpack-dev-server --inline --hot --open",
+    "build": "webpack"
  },

```

- Let's install locally TypeScript:

 ```
 npm install typescript --save-dev
 ```

- We need as well to drop a **tsconfig.json** file in the root folder of our project

 ```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "es6",
    "moduleResolution": "node",
    "declaration": false,
    "noImplicitAny": false,
    "jsx": "react",
    "sourceMap": true,
    "noLib": false,
    "suppressImplicitAnyIndexErrors": true
  },
  "compileOnSave": false,
  "exclude": [
    "node_modules"
  ]
}
 ```

 - Now, we need to transpile ES6 to ES5. Let's install **babel-core** and **babel-preset-env**.


 ```
 npm install babel-core babel-preset-env --save-dev
 ```

 - Babel needs to be configured for works. We will create one file **.babelrc** in root and later we will see how to put it in **webpack.config.js**. In this example, we will use this .babelrc: 

 ```json
 {
  "presets": [
    [
      "env",
      {
        "modules": false
      }
    ]
  ]
}
 ```

- Let's install bootstrap:

 ```
 npm install bootstrap --save
 ```



- Now, our **package.json** file should looks something like:

 ```json
{
  "name": "samplereact",
  "version": "1.0.0",
  "description": "Sample working with React,TypeScript and Webpack",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --inline",
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "awesome-typescript-loader": "^3.1.2",
    "babel-core": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "css-loader": "^0.28.7",
    "extract-text-webpack-plugin": "^3.0.0",
    "file-loader": "^0.11.2",
    "html-webpack-plugin": "^2.24.0",
    "style-loader": "^0.18.2",
    "ts-loader": "^2.0.3",
    "typescript": "^2.0.6",
    "url-loader": "^0.5.7",
    "webpack": "^3.6.0",
    "webpack-dev-server": "^2.4.2"
  },
  "dependencies": {
    "bootstrap": "^3.3.7"
  }
}
```

- Let's create a subfolder called **src**.

 ```sh
 mkdir src
 ```

- Let's create a basic **main.ts** file (under **src** folder):

 ```javascript
 document.write("Hello from main.ts !");
 ```

- Let's create a basic **index.html** file (under **src** folder):

 ```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    <div class="well">
      <h1>Sample app</h1>
    </div>
  </body>
</html>
 ```

- Now it's time to create a basic **webpack.config.js** file, this configuration will
 include plumbing for:
 - Launching a web dev server.
 - Transpiling from TypeScript to JavaScript.
 - Setup Twitter Bootstrap (including fonts, etc...).
 - Generating the build under a **dist** folder.

 ```javascript
let path = require('path'); 
let webpack = require('webpack'); 
let HtmlWebpackPlugin = require('html-webpack-plugin'); 

let ExtractTextPlugin = require('extract-text-webpack-plugin'); 

let basePath = __dirname;

module.exports = {
  context: path.join(basePath, "src"),
  resolve: {
      extensions: ['.js', '.ts', '.tsx']
  },

  entry: [
    './main.ts',
    '../node_modules/bootstrap/dist/css/bootstrap.css'
  ],
  output: {
    path: path.join(basePath, 'dist'),
    filename: 'bundle.js'
  },

  devtool: 'source-map',

  devServer: {
       contentBase: './dist', // Content base
       inline: true, // Enable watch and live reload
       host: 'localhost',
       port: 8080,
       stats: 'errors-only'
  },

  module: {
    rules: [
      {
        test: /\.(ts|tsx)$/,
        exclude: /node_modules/,
        loader: 'awesome-typescript-loader',
        options: { 
          useBabel: true, 
        }, 
      },
      {
        test: /\.css$/,
        include: /node_modules/,
        loader: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: {
            loader: 'css-loader',
          },
        }),
      },
      // Loading glyphicons => https://github.com/gowravshekar/bootstrap-webpack
      // Using here url-loader and file-loader
      {
        test: /\.(woff|woff2)(\?v=\d+\.\d+\.\d+)?$/,
        loader: 'url-loader?limit=10000&mimetype=application/font-woff'
      },
      {
        test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/,
        loader: 'url-loader?limit=10000&mimetype=application/octet-stream'
      },
      {
        test: /\.svg(\?v=\d+\.\d+\.\d+)?$/,
        loader: 'url-loader?limit=10000&mimetype=image/svg+xml'
      },
      {
        test: /\.eot(\?v=\d+\.\d+\.\d+)?$/,
        loader: 'file-loader'
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        loader: 'file-loader',
        options: {
          name: 'assets/img/[name].[ext]?[hash]'
        }
      },   
    ]
  },
  plugins: [
    // Generate index.html in /dist => https://github.com/ampedandwired/html-webpack-plugin
    new HtmlWebpackPlugin({
      filename: 'index.html', // Name of file in ./dist/
      template: 'index.html', // Name of template in ./src
      hash: true
    }),
    new ExtractTextPlugin({
      filename: '[chunkhash].[name].css',
      disable: false,
      allChunks: true,
    }),
  ]
}
 ```

- Run webpack with:

 ```
 npm start
 ```