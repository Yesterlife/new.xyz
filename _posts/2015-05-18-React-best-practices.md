I've spent a good deal of past year working with React. I've written, refactored, and re-written many components in that time, and I've seen some best pratices and anti-patterns emerge.

I'm not going to get into what React is or why you should use it - there are plenty of articles about React floating around the internet. I'm also going to assume you know the basics of working with React and have written a component or two yourself.

### USE THE PureRenderMixin

The PureRenderMixin is a mixin that overrides shouldComponentUpdate and only re-renders the components if the props or the state have actually changed. It is a pretty big optimization on top of React's already good performance. It also means you can call setState very often without worrying about spurious re-renders. You don't have to litter your code with the checks like these:

```javascript
    if (this.state.someVal !=== computedVal){
        this.setState({
            someVal: computedVal
        });
    }
```

The mixin requires that your `render()` method must be 'pure'. It must output the exact same output given the exact same props and state. This means you can't use direct class properties in `render()`, or render something differently when a property first changes to a value.

```javascript
    render: function(){
        // ...
        if (this._previousFoo !== this.props.foo){ // => impure
            return renderSomethingDifferent();
        }
    }
```
Using the `PureRenderMixin` usually isn't a problem in practice. I've added in to serval components after the fact and they continued to work normally. If you do add it and thing break, you should refactor the component until it work - tipically you've doing something odd.

One important detail is that it only does a shallow comparison of nextProps and nextState. If you have fields that are Objects or Arrays , modifying a property of one or pushing a new value will not trigger a component update, since the original reference does not change. Instead, you must make it so changing an object in your `props` returns a new reference to a new, updated object.

There are many strategies for accomplishing this. [Immutable.js](http://facebook.github.io/immutable-js/) introduct new inmmutable data types. If you want to work with plain Javascript objects, you can use React's [immutability helpers](http://facebook.github.io/react/docs/update.html) to prevent that forzen Javascript objects are persistent data collections.












































