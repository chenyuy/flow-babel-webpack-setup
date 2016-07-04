# flow-babel-webpack-setup
A tutorial for how to setup flow, babel and webpack for your project

1. **Initialize your project:<br />**
  Create a directory for your project and a `package.json` for it. Answer the questions from `npm init` as you see fit for you project.
  ```bash
  mkdir my_project
  cd my_project
  npm init
  ```
  
2. **Setup `flow`:<br />**
  Add the following line to your `scripts` property in your `package.json`:
  ```
  {
    "scripts": {
      "flow": "flow; test $? -eq 0 -o $? -eq 2"
    }
  }
  ```
  Back to your command line, run these to add flow to your project:
  ```bash
  touch .flowconfig
  npm install --save-dev flow-bin
  ```
   
3. **Test `flow`: <br />**
  Create a test file `print.js`:
  ```javascript
  var print = function(message: string): void {
    console.log(message);
  }
  ```
  Back to your command line and run `flow`:
  ```bash
  npm run-script flow
  ```
  It should print something like this:
  ```bash
  ...
  Spawned flow server (child pid=14597)
  Logs will go to /tmp/flow/zShomezSashisusdzSprojectszSjszSmy_project.log
  No errors!
  ```
   
4. **Setup `webpack`:<br />**
  First install `webpack`:
  ```bash
  npm install --save-dev webpack
  ```
  Move files before bundle to a new directory and create a new directory to hold bundled files:
  ```
  mkdir src
  mkdir bin
  mv print.js src/
  ```
  Add this to the end of `print.js` to make it a module:
  ```javascript
  module.exports = print;
  ```
  Create an entry file `src/app.js` for `webpack`:
  ```javascript
  var print = require('./print');
  print('hello world');
  ```
  Add a configuration file `webpack.config.js` for `webpack`:
  ```javascript
  module.exports = {
    entry: './src/app.js',
    output: {
      path: './bin',
      filename: 'app.bundle.js'
    }
  };
  ```
   
5. **Setup `Babel`:<br />**
  Install `Babel`, presets, loader and babel plugin for flow:
  ```bash
  npm install --save-dev babel-core babel-preset-es2015 babel-loader babel-plugin-transform-flow-strip-types
  ```
  Create a `.babelrc` file in project root directory:
  ```javascript
  {
    'presets': ['es2015'],
    'plugins': ['transform-flow-strip-types']
  }
  ```
  
6. **Put `Babel` and `webpack` together:<br />**
  Modify `webpack.config.js` to use `babel-loader`:
  ```
  module.exports = {
    entry: './src/app.js',
    output: {
      path: './bin',
      filename: 'app.bundle.js',
    },
    module: {
      loaders: [{
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
      }]
    }
  }
  ```

7. **Test everything:<br />**
  First modify `app.js` to use `es2016` features:
  ```javascript
  const print = require('./print');
  print('hello world');
  ```
  In your command line, run
  ```
  webpack
  node bin/app.bundle.js
  ```
  It should print out `hello world`. Or you can include `bin/app.bundle.js` in your html file to use it.

8. **Notes:<br />**
  - If you are using features not included in `es2015`, you may need to include additional babel plugins. For example, to use class property transform, you need `babel-plugin-transform-class-properties`.
  - You can add an additional optimization step to minify the bundled js files. For example, see [here](https://webpack.github.io/docs/usage.html#using-plugins) for adding `Uglify` to optimize the output.
