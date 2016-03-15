---
title: Using Webpack with hot reload for React and TypeScript
lang: en
aliases: [2015/11/04/webpack-hot-reloading-with-react-and-typescript]
---

JavaScript is growing fast and the build systems evolves. It's then hard to choose the right tool in this plethora of tools.

In my project I choose to use [Webpack](https://github.com/webpack/webpack), mainly because I want [hot reloading](https://github.com/gaearon/react-hot-loader) which basically means that when I update a component, the component will get replaced without a whole page refresh. Webpack also felt like an ease to use and I wanted to try it out.

Another thing I wanted to use was TypeScript, because of the static typing. It helps to catch early errors and will give me better suggestions from the code editor.

Using Webpack with TypeScript wasn't all ease... I knew that TypeScript had to be compiled before ES6 for it to work, so basically I sat the order to be:
>Run babel and then the ts compiler.

This didn't work out very well though...

I knew I had to have a `.babelrc` file that specified my presets so babel basically knew what to do. But somehow the Webpack loader did not get that configuration from the `.babelrc` file. After some documentation lookups I found that the `query` property could be passed to the loaders.

So what I did was to pass almost the same that I had in my `.babelrc` file. That is:

```javascript
'babel?' + JSON.stringify({
  presets: ['react', 'es2015', 'stage-1'],
}),
```

Now TypeScript worked just fine. Now the only thing left was hot reload.

So I was running the Webpack dev server with the `--hot` parameter already (`webpack-dev-server --hot --inline --port 3000`), and the only thing I needed to do was to add the `'react-hot'` loader to the list of loaders:

```javascript
loaders: [
  {
    test: /\.ts(x?)$/,
    exclude: /node_modules/,
    loaders: [
      'react-hot',
      'babel?' + JSON.stringify({
        presets: ['react', 'es2015', 'stage-1'],
      }),
      'ts',
    ],
  },
```

And now the hot reload worked very well together with React and TypeScript.

But as I have noticed in projects using TypeScript it's very easy to get conflicts with TypeScripts configuration files. To avoid this I added a `tsconfig.base.json` which stores my configuration. And to copy the contents of this file over to the "real" `tsconfig.json` file I added a NPM script for that in the setup phase: `"setup:tsconfig": "cp tsconfig.base.json tsconfig.json",`

The same goes for custom typings. I added a `typings_custom` folder which will contain my own typings. That is if I want a library that does not have any typings at the **DefinitelyTyped** typing repository. And for that to work out I need a NPM script that looks something like this:  `"setup:typings_custom": "cp -a typings_custom/* typings/",`.

And that's it.

For full configuration checkout this [gist](https://gist.github.com/drager/5f190a170bd55d415150).
