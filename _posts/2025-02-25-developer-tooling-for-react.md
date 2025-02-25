---
title: "Developer tooling for React"
date: 2025-02-18 00:00:00 +0800
categories: [React,Typescript]
tags: [react,typescript]
---
## Linting

**ESLint** rules (https://eslint.org/docs/latest/rules)—Includes a list of configurations included in the ESLint recommended ruleset

**Airbnb** style guide (https://github.com/airbnb/javascript)—Contains information about how to use it in ESLint as well

```sh
npm init @eslint/config
```

This file is named <root>/.eslintrc.{js,yml,json} (with the extension following your choice of format)

```json
{
  "extends": "eslint:recommended",
}
```

Also, you can include rules by listing them in a rules block:

```json
{
  "rules": {
    "semi": ["error", "always"],
    "quotes": ["error", "double"]
  }
}
```
Example eslintrc.cjs

```typescript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react/jsx-runtime",
    "plugin:react-hooks/recommended",
  ],
  ignorePatterns: ["dist", ".eslintrc.cjs"],
  parserOptions: { ecmaVersion: "latest", sourceType: "module" },
  settings: { react: { version: "18.2" } },
  plugins: ["react-refresh"],
  rules: {
    "react/prop-types": "off",
    "react-refresh/only-export-components": [
      "warn",
      { allowConstantExport: true },
    ],
  },
};
```

## Formatting
One of the most common ways is to format the object as JSON and store it in a file named .prettierrc or .prettierrc.json located at the root of your project. 

See more https://prettier.io/docs/en/configuration.html.

Suppose that you want to format your files with Prettier, but you want your project to use tabs rather than the default spaces, and you want to exclude semicolons at the end of statements.

```sh
$ prettier --use-tabs --no-semi src/
```

Alternatively, you can create a file located at <root>/.prettierrc.json with the following contents:

```
{
  useTabs: true,
  semicolon: false
}
```

You use npm for this step
```sh
npm install --save-dev prettier
```

## Property

**.propTypes is no longer supported as of React 19 and will be ignored.**

```tsx
import PropTypes from 'prop-types';
function Input({ name, label, value, onChange }) {
  return (
    <label>
      {label}
      <input name={name} value={value} onChange={onChange} />
    </label>
  );
}
Input.propTypes = {
  name: PropTypes.string,
  label: PropTypes.string,
  value: PropTypes.string,
  onChange: PropTypes.func,
};
export default Input;
```

### Specific types
```tsx
function UserDisplay({ user }) {
...
}
UserDisplay.propTypes = {
  user: PropTypes.shape({
    name: PropTypes.string,
    age: PropTypes.number,
  }),
};

function Users({ userList }) {
...
}
Users.propTypes = {
  userList: PropTypes.arrayOf(
    PropTypes.shape({
      name: PropTypes.string,
      age: PropTypes.age,
    }),
  ),
};
```
### Required properties

```tsx
function Car({ make, model, hasSunroof = false }) {
...
}
Car.propTypes = {
  make: PropTypes.string.isRequired,
  model: PropTypes.string.isRequired,
  hasSunroof: PropTypes.bool,
};
```

```sh
npm install --save prop-types
```

## Debugging
For Chrome, Firefox, and Edge, React Developer Tools can be installed as a plugin directly from the browser’s plugin store:

 - Chrome: https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?pli=1
 - FireFox: https://addons.mozilla.org/en-US/firefox/addon/react-devtools/
 - Edge: https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil

For other browsers, including Apple’s Safari, you have to do three things to get the plugin running:

```sh
$ npm install -g react-devtools
$ react-devtools
```
(which for most applications in this book requires updating the file in <root>/index.html)

```html
<script src="http://localhost:8097"></script>
```
This approach has some drawbacks.

Alternatives and other tools:
- If you’re using Redux or any of the derived libraries (such as Redux Toolkit or RTK-Query), Redux DevTools is a must-have for debugging. You can find it at https://github.com/reduxjs/redux-devtools.
- If you’re using React Native, the React Native Debugger is a great bridge application that allows you to use tools such as React Debugging Tools even while running React Native applications. You can get it at https://github.com/jhen0409/react-native-debugger.
- If you’re using Electron to build React apps as desktop applications, Reactotron is the library for you. It works as a bridge between your Electron-wrapped application and the regular developer tools, like the React Native Debugger. Get it at https://github.com/infinitered/reactotron.
- If you need to re-create and debug errors experienced by other people, consider using Replay.io. This tool is a freemium service, but at the free level, you’re able to debug only locally. If you subscribe at higher pricing tiers, you can re-create and debug error sessions experienced by other users. Even at the free tier, it’s an awesome debugging tool inspired by Redux DevTools, but it works regardless of your state management library. Read more at https://www.replay.io.