---
title: "Typescript 5"
date: 2025-02-18 00:00:00 +0800
categories: [Typescript]
tags: [typescript]
---

third-party package

https://github.com/SBoudrias/Inquirer.js

```sh
npm install inquirer@9.1.4
```

Installing type definitions

```sh
npm install --save-dev @types/inquirer@9.0.3
```

See https://github.com/DefinitelyTyped/DefinitelyTyped for the details of the Definitely Typed project and the packages for which type declarations are available.

Persistently storing data

```sh
npm install lowdb@5.1.0
```

See https://github.com/typicode/lowdb for details if you want to use Lowdb in your projects.

The undefined type is used when variables are defined but have not been assigned a value

![Table 3-1](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_3-1.png)

When the typeof keyword is used on null values, the result is object. This is a long-standing behavior that dates back to the earliest days of JavaScript and that hasn’t been changed because so much code has been written that expects this behavior.

![Table 4-1](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_4-1.png)

You should use ECMAScript modules for most projects, just as long as they are supported by your JavaScript runtime, such as Node.js or your browser. Not only are ECMAScript modules the “real” standard, but an ECMAScript module can import from a CommonJS module, which means that you can mix and match module formats.

Creating the package.json file
```sh
npm init --yes
```

Adding packages using the Node Package Manager
```sh
npm install --save-dev typescript@5.0.2
npm install --save-dev tsc-watch@6.0.0
```

Compiling the TypeScript code
```sh
tsc
```

Running the compiled code
```sh
node dist/index.js
```

![Table 5-1](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-1.png)


![Table 5-3](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-3.png)

![Table 5-4](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-4.png)

![Table 5-5](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-5.png)

Displaying the list of files for compilation
```sh
tsc --listFiles
```

Starting the compiler in watch mode
```sh
tsc --watch
```

Starting the package command
```sh
npx tsc-watch --onsuccess "node dist/index.js"
```

See https://github.com/gilamran/tsc-watch for details of the other options provided by the ts-watch package.

The TypeScript compiler also provides an API that can be used to create custom tools, which can be useful if you need to integrate the compiler into a complex workflow. Microsoft doesn’t provide extensive documentation for the API, but there are some notes and examples at https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API.

![Table 5-6](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-6.png)

![Table 5-7](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-7.png)

![Table 5-8](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-8.png)

![Table 5-9](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-9.png)

See https://www.typescriptlang.org/docs/handbook/compiler-options.html for the complete set of options the compiler supports.

![Table 5-10](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_5-10.png)

Starting the Node.js debugger
```sh
node inspect dist/index.js
```

Starting Node.js in remote debugger mode
```sh
node --inspect-brk dist/index.js
```

The standard TypeScript linter used to be TSLint, but this has been deprecated in favor of the typescript-eslint package.

```sh
npm install --save-dev eslint@8.36.0
npm install --save-dev @typescript-eslint/parser@5.55.0
npm install --save-dev @typescript-eslint/eslint-plugin@5.55.0
```

![Table 6-1](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_6-1.png)

Adding packages to the project
```sh
npm install --save-dev jest@29.4.3
npm install --save-dev ts-jest@29.0.5
npm install --save-dev @types/jest@29.4.0
```

![Table 6-2](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_6-2.png)

Starting the unit test framework in watch mode
```sh
npx jest --watchAll
```

![Table 7-1](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_7-1.png)

![Table 7-2](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_7-2.png)

## Using functions

![Table 8-2](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_8-2.png)


![Table 13-3](https://drek4537l1klr.cloudfront.net/freeman3/HighResolutionFigures/table_13-3.png)