---
title: Using Waffle for project management
lang: en
alias: [2015/11/04/using-waffle-for-project-management]
---

In my project I'll be using [Waffle](https://waffle.io/) for project management. Waffle provides a project management board with an minimalistic interface, unlike other project management boards. It's not cluttered with stuff that you don't need and it integrates very well with Github where most of your issues already are present.

Waffle is easy to setup and use. Just head over at https://waffle.io/ and sign in with your Github account. Then just choose to create a new board and choose the repository you want to be waffled.

That's pretty much it. Just start writing issues and simply close them using a nice message when committing. Some message can be like this:
```
git commit -m "closes #1"
git commit -m "fixes #2"
git commit -m "resolves #3"
```
These messages are just a few keywords Waffle uses. Check out the Waffle [website](https://waffle.io/) for more information regarding Waffle.

When submitting a pull request it should follow the same principle as above. That is, providing a message containing a keyword and the number of the issue it fixes. If you provide a Waffle keyword in your commit message the issue card and the pull request card will be merged together. It will also mark the issue as **in progress**.

This is powerful when working in somewhat larger teams, especially for open source projects.
