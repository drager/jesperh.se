---
title: Reactive extensions in Javascript
lang: en
---

I have done my deep dive in Reactive Extensions, abbreviated called just Rx. And Rx is a library created by Microsoft. It exists in many different languages such as Java, C#, C, Javascript and many other languages. The Javascript version is where I have put my focus.

First just let's just go through what an observable is.

# Observable
An observable is a stream of events. It's not that different from an array, except that the items in an observable happens over time, asynchronously. So we can think of an observable as an asynchronous array.

And what Rx let's us do is act on an observable just as we act on an array. So we can use all the awesome array functions that we know. Such as `map`, `filter`, `reduce` and so on. Rx has even some more functions such as `zip`, `flatMap`, `takeUntil` to name a few.

So for example, take an array like this:
```javascript
[1, 2, 3].forEach(item => console.log(item));
```
Here we are using the array method forEach and the items are logged out right away. We can use the forEach method on an observable as well, but with an observable, forEach executes over time, that is asynchronously.
```javascript
const observable = Rx.Observable.interval(500).take(4);

observable.forEach(item => console.log(item))
```
Here the numbers 0, 1, 2, 3 are logged out to the console one by one each 500 ms.

Moving on to creating an observable from scratch. To do that we can use the `Rx.Observable.create()` method and it’s not so very different from creating an promise:
```javascript
// Creating a promise
const promise = new Promise((resolve) => {
  setTimeout(() => {
    resolve(10);
  }, 300);
});

// Would console log 10 after 300 ms
promise.then(value => console.log(value));
```
And for creating an observable:
```javascript
// Creating an observable
const observable = Rx.Observable.create((observer) => {
  setTimeout(() => {
     observer.onNext(10);
  }, 300);
});

// Would console log 10 after 300 ms
observable.forEach(value => console.log(value));
```
An observable is very familiar to a promise. But an observable can return many values instead of one value that a promise can emit. There's a very little difference in syntax as you can see. The difference here is that we are using `forEach` on an observable instead of `then`.

A technical difference between promises and observables is that observables are lazy which means that they does not start producing data until they are "listened" to. The "listening" is called a subscription. An observable can also emit many values and a promise can only emit one value. A promise cannot be canceled and will not tell when it's completed, which an observable can.

To show of the "laziness", take this code for example:
```javascript
// This would console log 'promise'
const promise = new Promise((resolve) => {
  console.log('promise');
});

// This would not console log 'observable' before we have subscribed to it.
const observable = Rx.Observable.create((observer) => {
  observer.onNext(10);
  console.log('observable');
  observer.onCompleted();
});
```
The observable would not console log out 'observable' but the promise would console log out 'promise'. As I said before, we need to subscribe to the observable for it to start emitting values. And to do that we simply call the `subscribe` or the `forEach` method on the observable. Just like this:
```javascript
observable.subscribe(
  // Will log the number 10
  (x) => console.log(x),
  // Would log the error if there was one
  (error) => console.error(error),
  // Will log 'completed'
  () => console.log('completed')
);
```
And now the observable starts to emit values and
the first argument for the subscribe method is the next handler which is called when the observable emits a value like this: `observer.onNext(10);`. The second argument is the error handler which is called if observable has encountered some error and the last argument is the complete handler which is called when the `onCompleted` method is called, that is after it has called onNext for the final time, if it has not encountered any errors.

We can also unsubscribe from observables (unlike promises). To unsubscribe just call the `dispose` method on the subscription, like this:
```javascript
const observable = Rx.Observable.interval(500).take(4);

const subscription = observable.subscribe(
  (value) => console.log(value)
);

// Unsubscribe after 1500 ms
setTimeout(() => {
  subscription.dispose();
}, 1500);
```
Now we wont get any more data after the 1500 ms has gone and it will console log out:
```javascript
0
1
2
```
And nothing more than that. (If we would not have unsubscribed we should have seen: 0, 1, 2, 3).

That's just a basic example how we can create an observable from scratch, subscribe to it and unsubscribe from it. We can also convert existing events, arrays and promises into observable collections using methods like: `Rx.Observable.fromEvent()`, `Rx.Observable.fromArray()`,
`Rx.Observable.fromPromise()`.

Here's a simple example that creates an observable collection of events from the button element and the click event:
```javascript
const buttonElement = document.querySelector('button');

const observable = Rx.Observable.fromEvent(buttonElement, 'click');

observable.forEach(
  (item) => console.log('clicked')
);
```
This would console log 'clicked' every time we click on the buttonElement.

Another example that creates an observable collection from an promise:
```javascript
const observable = Rx.Observable.fromPromise(
  fetch('https://api.github.com/repositories')
    .then(response => response.json()));

observable.forEach(
  (json) => {
    console.log(json);
  },
  (error) => console.log(error),
  () => console.log('completed')
);
```
This would console log the response as json, log an error if that have accorded during fetching.

# Why use RxJs?
Since error handling in asynchronous programming is hard, one reason to use Rx is because it makes it simpler to handle errors in asynchronous programming. We can also know when an observable is completed, that is when the observable is done emitting values, which is really great feature. Another thing that speaks for using observables is that an observable can emit more than 1 value, unlike promises that can only emit 1 value. Another feature that is great is that an observable is cancellable. It's also easy to build powerful asynchronous apps with little and expressive code, using methods that we already know.

I think that it's really cool and fun to build asynchronous apps using RxJs and that's really a great thing for why I want to use it.

# Concrete examples
To really show the power of Rx, I will show two concrete examples with Rx in action.

First out is a drag and drop:
<a class="jsbin-embed" href="http://jsbin.com/yeguqerohi/embed?js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.9"></script>

To walk through this code. First we're using Rx's fromEvent method that creates an observable collection of events from the given element and the given event as seen in the code, `mouseup`, `mousedown` and so on. Then we `map` over all the mousedown collection and for each mousedown we map over the mousemove collection and for each mousemove we return an object containing the left and top position of the element. The `takeUntil` method will let the mousemoves collection to produce values until the mouseups collection produces a value. So the mousemoves will stop emitting values when the mouseups starts to emit a value. This results in a two-dimensional array and therefore we need to flatten it by using `concatAll`.

We then subscribe to this observable collection which gets an object containing the left and top of the element and finally we move the element.

The second example is an autocomplete that queries the Github API:
<a class="jsbin-embed" href="http://jsbin.com/gupenejoza/embed?js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.9"></script>

And here is the fromEvent method used as well. We then map on the keypresses to get the value from the input. Then we debounce, which results in that we will only get the value after 250 ms (ignoring the first characters). Then we're using the `distinctUntilChanged` method which will remove arrows and other control characters. Then  we're mapping over each value and call the githubApi function with the value we entered in the input. Then the githubApi function does a request with value as query and returns the data as json. This will give us an a two-dimensional array and therefore we need to flatten it by using `concatAll`. Then we can just forEach over the result and add it to the dom.

As you can see, it's not so much code at all to do some really powerful stuff.

# Conclusion
I think RxJs is really great and will keep on using it because I really enjoy doing asynchronous programming this way. I hope I have been able to spread some of my joy and knowledge about Rx through this post. And if you want to learn more about Reactive extensions, check out Jafar's excellent talk about Reactive Extensions: [Async JavaScript with Reactive Extensions](https://www.youtube.com/watch?v=XRYN2xt11Ek) and the [rx-book](http://xgrommx.github.io/rx-book/).
