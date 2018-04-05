# package.json

`package.json` (create and place in root of soccer project)

```json
{
  "name": "soccer-stars-react",
  "version": "0.0.1",
  "private": true,
  "devDependencies": {
    "autoprefixer-stylus": "0.10.0",
    "babel-eslint": "^7.1.1",
    "concurrently": "3.0.0",
    "eslint": "^3.12.2",
    "eslint-plugin-flowtype": "^2.29.1",
    "eslint-plugin-jsx-a11y": "^3.0.2",
    "eslint-plugin-react": "^6.8.0",
    "react-scripts": "0.6.1",
    "stylus": "0.54.5"
  },
  "dependencies": {
    "history": "4.2.0",
    "re-base": "2.2.0",
    "react": "15.3.2",
    "react-addons-css-transition-group": "15.3.2",
    "react-dom": "15.3.2",
    "react-router": "4.0.0-alpha.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "watch": "concurrently --names \"webpack, stylus\" --prefix name \"npm run start\" \"npm run styles:watch\"",
    "build": "react-scripts build",
    "eject": "react-scripts eject",
    "styles": "stylus -u autoprefixer-stylus ./src/css/style.styl -o ./src/css/style.css",
    "styles:watch": "stylus -u autoprefixer-stylus -w ./src/css/style.styl -o ./src/css/style.css"
  },
  "eslintConfig": {
    "extends": "./node_modules/react-scripts/.eslintrc"
  }
}
```

* `react-scripts` - what `Create React App` uses. If we started with Create React App and just used that we would get some errors because Create React App is using an older version of React Router and this is using an alpha version of React Router 4. Using our `package.json` locks in all the exact versions we need of our dependencies and dev dependencies to ensure our app works

### Install with npm
`$ npm install`

### Run our server
`$ npm start` - This is in `package.json` - `react-scripts start`

`$ npm watch` - Will run our start command and watch our styles

`$ npm build` - For production

`$ npm eject` - Remove from create-with-react