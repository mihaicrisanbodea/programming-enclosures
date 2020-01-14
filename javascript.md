## JavaScript

- [Rest parameter](#restparameter)
- [Stack trace](#stacktrace)

### Rest parameter

**An indefinite number of parameters** can be represented using the rest parameter, starting with ECMAScript 2015.<br/>
It could be the only argument of a function, or alongside other arguments.
```javascript
function getLength(...arguments) {
  return arguments.length;
}
```
___


### Stack trace

**Debugging** might be trickier in JavaScript. There are a few ways of inspecting the current execution:
`arguments.callee.caller` can be used to see what is the caller function <br/>
`console.trace()` for displaying the stack trace <br/>
`.stack` property of the `Error` object <br/>
___


### Polyfills

**Promise**s might still not be natively supported in certain browsers (e.g. in IE), so a polyfill might be needed.
```javascript
if (typeof Promise !== "function") {
  this.Promise = function(handler) {
    var self = this;
    self._handler = handler;
    this.then = function(onFulfillment, onRejection) {
      this._handler(onFulfillment, onRejection);
    }
    return self;
  }
}
```
___
