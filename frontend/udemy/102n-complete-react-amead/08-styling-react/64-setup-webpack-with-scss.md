# Setup Webpack with CSS
## Add css rule to `package.json`

* **note** search packages using this url pattern:

`https://npmjs.com/package/NAMEOFPACKAGEHERE`

### css-loader
* [link to css-loader](https://www.npmjs.com/package/css-loader)
* This will help load in our css assets
* It will teach it how to take css and convert it into a JavaScript representation of that css
* We will also use the **style-loader**
    - [link to style-loader](https://www.npmjs.com/package/style-loader)
* Takes the CSS that is in JavaScript and adds it to the DOM by injecting a style tag into our content

`$ yarn add style-loader css-loader -D`

```json
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        loader: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/,
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  devtool: 'cheap-module-eval-source-map',
  devServer: {
    contentBase: path.join(__dirname, 'public'),
  },
};
```

* We check for any files that have a `.css` extension
* We add our css loaders
* That's all it takes to setup CSS inside of webpack

### Summary
* The rule says whenever webpack encounters a `.css` file it will read that file in with `css-loader` and it will dump its contents into the DOM using `style-loader`
    - End result === our styles showing up

## Import our css file
`import './styles/styles.css'`

* We don't need a from and we don't need a named export or default export
* We are just loading the entire file into our main app (app.js)

`app.js`

```
import React from 'react';
import ReactDOM from 'react-dom';

import IndecisionApp from './components/IndecisionApp';
import './styles/styles.css';

ReactDOM.render(<IndecisionApp />, document.getElementById('app'));
```

* And here is our test css
* We'll make all the font in our app red... (Yes it's ugly but we'll fix it soon)

`src/styles/styles.css`

```css
* {
  color: red;
}
```

## Run the server
`$ yarn run dev-server`

* View in browser and all text in app is red
* That's all we need to set up css inside webpack

### Efficiency Question
* Is webpack really waiting for all the JavaScript before it sets up our styles?
    - Yes
    - Later we'll create our own css file and make this more efficient

## SCSS (sass)
### How can we set it up with webpack to transpile down to CSS?
* We need a new loader that has rules on how to handle `.scss` files
* And we'll need the `.scss` compiler itself

## Tweak our existing CSS rules
* We will change looking for `.css` files and change it to `.scss` files

`app.js`

```
import React from 'react';
import ReactDOM from 'react-dom';

import IndecisionApp from './components/IndecisionApp';
import './styles/styles.scss';

ReactDOM.render(<IndecisionApp />, document.getElementById('app'));
```

### We need to test our .scss so:
* Rename `styles.css` to `styles.scss`
* Update the code so we see that it changes and works

`styles.scss`

```
$blue: blue;

* {
    color: $blue;
}
```

## Update our webpack config file
`webpack.config.js`

```js
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        loader: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/,
      },
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  devtool: 'cheap-module-eval-source-map',
  devServer: {
    contentBase: path.join(__dirname, 'public'),
  },
};
```

* Run the server `$ yarn run dev-server`
* And we'll get an error like:

![loader error for scss](https://i.imgur.com/ODqP7fc.png)

* We can't load in `styles.scss` to the browser as browsers don't understand `.scss` files
* How can we transpile the `scss` file to `css`?

### Install sass loaders needed for transpiling scss to css
`$ yarn add sass-loader node-sass -D`

* Update our code to transpile scss to css

`webpack.config.js`

```js
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        loader: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/,
      },
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
      },
    ],
  },
  devtool: 'cheap-module-eval-source-map',
  devServer: {
    contentBase: path.join(__dirname, 'public'),
  },
};
```

* We just added `sass-loader` to our **use** loaders array

## Test
`$ yarn run dev-server`

* All our font color should now be blue in the browser

## Add sass sourcemaps
* We need to know where our code is when we view it in the browser
* Checking now will show you only css is injected into style tags
* When we add sourcemaps we can see the exact .scss file and line number our styles are located inside
* Sourcemaps are essential for development but should not exist in production

### Adding sourcemaps is easy with webpack
`webpack.config.js`

```js
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        loader: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/,
      },
      {
        test: /\.s?css$/,
        use: [{
                        loader: "style-loader"
                    }, {
                        loader: "css-loader", options: {
                            sourceMap: true
                        }
                    }, {
                        loader: "sass-loader", options: {
                            sourceMap: true
                        }
                    }]
      },
    ],
  },
  devtool: 'cheap-module-eval-source-map',
  devServer: {
    contentBase: path.join(__dirname, 'public'),
  },
};
```

## Stylelint
* This helps find bugs in your css
* It also helps format your styles in alphabetical order

### Add these packages

`$ yarn add stylelint stylelint-order -D`

`/.stylelintrc`

```
{
  "plugins": [
    "stylelint-order"
  ],
  "rules": {
    "order/order": [
      "custom-properties",
      "declarations"
    ],
    "order/properties-alphabetical-order": true
  }
}
```

## Where is my bundle.js?
* webpack-dev-server is serving `bundle.js` from memory
* This is done to make serving `bundle.js` fast
* You can add another webpack config for production that spits out `bundle.js` to disk


