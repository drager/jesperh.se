---
title: Switching from gulp to npm scripts
lang: en
---

We have been using a large build configuration in our angular application using gulp. Everything  from transpiling ES6, compiling sass, concatenating files and minifying them to trigger live reload. This resulted in a lot of configuration files for each task. Dividing each task into its on file is better than having everything in a tremendous file so this configuration was fine but not the best option for us. The bad truth with this configuration was that one or two people knows how this works (that had set it up once), so that affected the whole team a bit.

Check out this [repository](https://github.com/drager/bean/tree/master/gulp) for an example of our gulp configuration.

## Enter NPM scripts
We hadn't heard much about using npm scripts for building our project but on [Nordic.js](http://nordicjs.com/) [Kate Hudson](http://blog.ibangspacebar.com/) from Mozilla had a great talk about it and convinced us to try it out. Once we tried it, we absolutely loved it! It was so easy and not as confusing as our large gulp configuration was. We were very pleased with the result and ease npm script had given us.

So we were doing almost the same as we did in our gulp configuration with less lines of code, more readability and everyone in the team understood what was going on. With our simple npm scripts we were transpiling ES7, compiling TypeScript, compiling sass and so on.

Here are our new configuration using npm scripts:
```
"scripts": {
    "postinstall": "tsd install",
    "start": "npm run build && npm run packager",
    "build": "npm-run-all build:*",
    "build:typescript": "tsconfig && tsc",
    "build:babel": "babel .tmp/ts --out-dir dist",
    "lint": "npm-run-all lint:*",
    "lint:typescript": "tslint `find app -name '*.ts?'`",
    "packager": "node_modules/react-native/packager/packager.sh --skipflow --root dist",
    "tsconfig": "tsconfig"
  }
```

Looks pretty neat and works perfect for our application though this configuration is not for an angular application its almost the same result as we wanted with our angular application.

With that said, try out npm scripts! You'll love it too.
