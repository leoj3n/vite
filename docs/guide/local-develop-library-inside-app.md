# Local Develop Library Inside App

Often times a developer using Vite to bundle their library would like to use that library inside an app also bundled by Vite that shares similar dependencies.

If the developer attempts to use `npm link` the library directory into the app, there will be duplicate versions of the shared dependencies because of how linked `node_modules` works.

Let's say both the library and the app both depend on React. There will be one version of React for the library, and then a second for the app.

These copies will be present in the file structure as literal separate copies of the React dependency files:

App directory:

```
.
├── node_modules
│   ├── custom-component-lib -> ../../custom-component-lib
│   ├── react
│   └── ...
├── package.json
├── src
│   ├── App.tsx
│   └── ...
└── yarn.lock
```

Custom component library directory:

```
.
├── babel.config.js
├── dist
│   ├── cjs
│   ├── esm
│   └── index.d.ts
├── node_modules
│   ├── react
│   └── ...
├── package.json
├── rollup.config.js
├── src
│   ├── assets
│   ├── components
│   └── index.ts
├── tsconfig.json
└── yarn.lock
```

In the case of React there will be a clear error message when attempting to run the app:

<img width="643" alt="image" src="https://user-images.githubusercontent.com/990216/156873032-57bb077f-2a35-4d42-aa0c-c8e3aa435678.png">

> Error
> Invalid hook call. Hooks can only be called inside of the body of a function component. This could happen for one of the following reasons:
> 1. You might have mismatching versions of React and the renderer (such as React DOM)
> 2. You might be breaking the Rules of Hooks
> **3. You might have more than one copy of React in the same app**
> See https://reactjs.org/link/invalid-hook-call for tips about how to debug and fix this problem.

There have been issues raised by developers about how to overcome this issue while keeping the conveinence of `npm link`:

<img width="648" alt="image" src="https://user-images.githubusercontent.com/990216/156872369-685bdb20-a643-42f0-a4d8-a6e46bea7104.png">

- https://stackoverflow.com/questions/65501715/vue-3-component-incorrectly-initialized-when-module-is-npm-linked

One option is to manually copy the necessary files to a "throw away" directory after building the library with Vite so that it does not include the `node_modules` and then link this directory instead.

<img width="645" alt="image" src="https://user-images.githubusercontent.com/990216/156872445-c5d4cba7-f0bb-477c-9c31-9d3fa397d58a.png">

- https://github.com/tiago-js-mota/react-proj/pull/1

While this works it is an extra step and feels hacky and would be nice to do without.

A better alternative is to use the latest version of Yarn with it's workspaces feature:

- https://github.com/tiago-js-mota/react-proj/pull/2
