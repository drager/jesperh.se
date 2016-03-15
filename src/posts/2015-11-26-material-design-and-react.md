---
title: Material design and React
lang: en
aliases: [2015/11/26/material-design-and-react]
---

Using material design with React is very easy and this blogpost will briefly go through using Material Design in React with material-ui.

[Material design](https://www.google.com/design/spec/material-design/) is Google's design specification/visual language.

Read more about the specification at Google's [website](https://www.google.com/design/spec/material-design/).

# Using Material Design with React
So how should we apply Material Design to our React project?

There's two ways that I know of. Either we could try to implement the whole specification from the ground, reading it upside-down. (Which we probably should do anyway, to know the specification). Or we could use [material-ui](https://github.com/callemall/material-ui) who has done this for us.

Material-ui is simply a set of React components that implement Material Design.

# Using material-ui
First of, just install the package from npm: `npm install material-ui --save`.

Material-ui was designed with the Roboto font in mind. So make sure to include it in your project as described [here](https://www.google.com/fonts#UsePlace:use/Collection:Roboto:400,300,500).

Now that we have the material-ui installed it's pretty straightforward to use these components which comes with material-ui. You can use the components this way in ES6 for example:

```javascript
import { Avatar, Card, CardHeader } from 'material-ui';

export class MySpecialCard extends React.Component<{}, {}> {

  render() {
    return (
      <Card>
        <CardHeader
          title='Special Card'
          subtitle='2015-11-25'
          avatar={<Avatar>SC</Avatar>}>
        </CardHeader>
      </Card>
    );
  }

```

This will give you a component that renders a Card with a CardHeader that contains an Avatar. As you can see, it's pretty easy to use these components.

This is just one of many components that material-ui has. All the components available can be found [here](http://material-ui.com/#/components/).

## Colors and typography

If we only want to use some colors or typography we can include them and use as well to create components that uses the colors and typography from the specification.

Again using ES6 we can do it like this:
```javascript
import * as Colors from 'material-ui/lib/styles/colors';
import * as Typography from 'material-ui/lib/styles/typography';
```

Now we have all the colors and typography that material-ui has implemented from the specification to our disposal.

And we can now do things like this:
```javascript
const styles = Object.freeze({
  avatar: {
    backgroundColor: Colors.red500,
    color: Colors.fullWhite,
    fontWeight: Typography.fontWeightLight,
  },
  doneIcon: {
    fontSize: 32,
    display: 'flex',
    color: Colors.green500,
  },
});
```

Which is a great way to get the colors and typography right when writing own components.

## Custom themes
With material-ui you can set up custom themes of your liking.

Create a custom `theme.jsx` file to build your theme. It could be something like this:
```javascript
import * as Colors from 'material-ui/lib/styles/colors';
import * as ColorManipulator from 'material-ui/lib/utils/color-manipulator';
import * as Spacing from 'material-ui/lib/styles/spacing';

export const CustomTheme = Object.freeze({
  spacing: Spacing,
  fontFamily: 'Roboto, sans-serif',
  palette: {
    primary1Color: Colors.blueGrey500,
    primary2Color: Colors.cyan700,
    primary3Color: Colors.lightBlack,
    accent1Color: Colors.pinkA200,
    accent2Color: Colors.grey100,
    accent3Color: Colors.grey500,
    textColor: Colors.darkBlack,
    alternateTextColor: Colors.white,
    canvasColor: Colors.white,
    borderColor: Colors.grey300,
    disabledColor: ColorManipulator.fade(Colors.darkBlack, 0.3),
  },
});
```

Then you can apply the custom theme like this:
```javascript
import { AppBar } from 'material-ui';
import * as ThemeManager from 'material-ui/lib/styles/theme-manager';
import { CustomTheme } from '../theme';

export class MySampleAppComponent extends React.Component<{}, {}> {
  getChildContext() {
    return {
      muiTheme: ThemeManager.getMuiTheme(CustomTheme),
    };
  }

  render() {
    return (
       <AppBar title='My Bar' />
    );
  }
}

MySampleAppComponent.childContextTypes = {muiTheme: React.PropTypes.object};
```
Read more about themes at material-ui's [website](http://material-ui.com/#/customization/themes).

That's pretty much it. It's easy to use and contains a handful of components but I'm not sure that you need the whole material-ui if you're building a simple little project.
