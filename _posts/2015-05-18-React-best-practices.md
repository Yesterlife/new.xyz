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


### USE PROP TYPES

As your application grows and you start composing components into complicated hierarchies, hunting down missing or improper props can be a pain. Luckily, React offers
a way to specify which types a component expects for each of its props and which props are required; PropTypes. Each React class can define a propTypes map that specifies
a validation function for each prop it is set to receive. If the validation function fails, a warning will be logged in console. If you forget a prop that isRequired or 
pass a string when an object is expected, you'll get a helpful message in the console.

PropTypes are also a convertient way to document what props your component expects and what they do.

Also the PropTypes checks are only done outside of production builds, do be sure to set NODE_ENV='prpduction' to prevent needlessly slowing down your final application.

Also, every propTypes that is not isRequired should have a corresponding field in getDefaultProps(). (This is not always feasible since getDefaultProps() is noly called
        once per createClass, not once per instance, meaning any object instances will be shared, getDefaultProps() only works well for primitive types.)


### AVOID STATE

'Avoid State' is a mantra that holds true to all of programming, and React components are no exception. Event though React commendably gives you facilities to work 
explicitly with state (setState, etc...) you should avoid  it.

State doesn't compose very well. It can lead to complicated components, convoluted components, and spurious re-renders, even with the PureRenderMixin. Probably the worst 
thing you can do it call setState right a render in componentDidMount() or componentDidUpdate(). While it it tempting read things from the generated DOM at this point, it 
means most render() calls will actually call render twice. If a child component does the same thing, it could have up to four render() calls: two from its parent's render
, and 2 from its own internal setState(). As you compose components this can lead to property/layout thrashing and exponentially more re-renders as your component 
hierarchy depth increase.

Granted, there still cased where you do need state, but when you do, only use state when it can be perfectly encapsultaed by the component. If a parent component needs to
know too much about some state on a child component, something has broken down in your component architecture - your abstraction has just become leakly. You should refactor.

### CENTRALIZE STATE


So what do you do when you have component state that is required, can't be fully encapsulated, and needs to be commucicated up to parent components? In this case, you 
should pull the state completely out of the component hierarchy and into a central state object. Have the state flow down through your components purely as props - have
no state actually managed by components. This is a central the of the Flux architecture -- have a set of Stores that hold your application state and event-driven Actions 
that modifiy the Stores -- and Om -- your entire application state is stored in a single atom, which  any change to it re-rendering the entire application. Uni-directional data-flow is the goal.

In pratice, this mean that your components must primarily rely on props. Instead of calling setState, they communicate with a central data store of some sort, either through events, streams, channels, callbacks, or calling methods directly. The central data store contains a hierarchical repersentation of the application state, and 
triggers a re-render on the entire application on every change. Uni-direction data flow. This may seem inefficient, but one of the mantras of React is built around 
intelligent and effcient updates to the DOM. If you have pure componets, you can optimize updated to the vitual DOM though the PureRenderMixin on top of React's already
stellar DOM optimization. 

This is all abut counter-intuitice, and is not really obvious reading the docs. But having tried it other ways, I realize it is the best way to create anything larger than a trivial application.

The benefit of centralized state and uni-directional state is that your application is easier to reason about. There is a single place where a piece of data can come from. Your app is simpler.

Here is a cude diagram to illustrate.
![flux data flow](/images/data_flow.svg)

This can be thought of as a simplified Flux architecture diagram (I've glossed over how exactly messaged from components get to the store and store triggers re-renders). All you need to know is that whenever the central store is updated, it calls render() with the new properties on the root component, and the updates propagate through its 
component hierarchy.

I've found that even if you do not set out to follow the Flux architecture directly, best practices will lead you to something that looks very similar.

This means your components are simple functions of props to DOM elements in most cases. They are mostly declaratice. Testing them is trivial - just verify the right DOM 
output given the right props, and verify the right messages sent after the right DOM events. No need for complicated mocking schemes. Bugs are easy to find - either a 
component doesn't render properly, doesn't trigger the right events, or your central object doesn't update properly.


When we call setState or have intra-component communication the diagram is not so pretty:
![state data flow](/images/data_flow2.svg)

The data flow loops back on itself, each prop no longer has a single origin. The application id fundamentally more complicated - whorls of confusion instead of single cycle. Components can re-render needlessly, and property thrashing is possible.

I also would say the pairs of components tnat communicate through callbacks and events directly are too tightly coupled - the parent component has to know too much about a child component down the line. This is a sign of leaky component abstraction. The lines between components probably should have been drawn differently.

You may also conjecture that which uni-directional data flow, the parent conponent is already coupled to its child component, since it has to manage the props it will need. I would say this "one-way coupling" is okay, it is in fact cohesion.

### MIXINS ARE AWESOME

Mixins are handy ways to create re-usable chunks of functionality that multiple components will use. I've already talked at length about how the PureRenderMixin overrides shouldComponentUpdate(). One cool feature of mixins is that they do not override component lifecycle methods, but rather, they are called in addition. This means a mixin can do setup in its own componentWillMount() method and clean up in componentWillUnmount() without conflicting with the host component's componentWillMount. In general, mixins are useful for isolating related chunks of functionality.

One thing I am experimenting with is putting all state-handling logic in a mixin, even if it is only used by one component. You can then mixin this logic to a parent component (or a central store, if you don't use lifecycle methods) and keep the underlying component stateless. You can then create a standalone stateful component with the mixin that is a thin wrapper around the stateless component. Use the stateful wrapper when it makes sense, and the state-less component with the mixin when you need to compose the component in a larger system.

Additionally, you can test mixins in isolation pretty easily. Either test the methods directly or use the mixin in a dummy React class





















































































