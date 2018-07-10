# Fetchq Gitbook Documentation Project

This documentation project uses [Gitbook Toolchain](https://toolchain.gitbook.com/), 
please have a read before you start contributing so you get a hang of it.

There are 2 basic twists from a normal _Gitbook_ project:

- `src` is used as the contents source folder
- `docs` is used as the build folder

We achieve that by customizing the `gitbook serve/build` command in the `package.json`.
Basically you need to use `yarn/npm start` and `yarn/npm build` to run the _gitbook_ properly.

## Start the book locally

```
yarn install
yarn start
```

the project will resolve dependencies and boot on port 4000.

[Open: http://localhost:4000](http://localhost:4000)

## Build the book for publication

```
yarn build
```

## How to contribute

1. fork
2. make a nice change / fix / contribution
3. commit and open a PR

We appreciate your help!

