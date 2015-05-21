---
layout: post
title: React Primer Draft.
---

### React Component

A React component encapsulates everything. It does not separate the view from the view logic, but rather merges the two together. Separating these doesn't really make
sense when building a user interface: the view and its view logic are inevitably tightly coupled. Rather than jumping between a template file and some of view-controller
it make sense to keep them together. React components are usually small enough that this is not a big deal to have the two together, and if it does get to be too large 
you can break down your component into smaller components.

> #### Components are Just State Machines
  
 React thinks of UIs as simple state machines. By thinking of a UI as being in various states and rending those states, it's easy to keep your UI consistent.

 In React, you simpley update a component's state, and then render a new UI based on this new state. React takes care of updating the DOM for you in the most effocient
 way.

 React will use event delegation behind the sences. So now we have a very declarative way to associate events to DOM elements. Now there's no confusion as to what 
 elements have what events and there is no hassle of managing binding hooks between DOM and javascript.

 > #### Event Handling and Synthetic Events
    
   With React you simple pass your event handler as a camelCased prop similar to how you'd do it in normal HTML. React ensures that all events behave identically in IE8 and above by implementing a synthetic event system. That is, React knows how to bubble and capture events according to the spec, and the events passed to your event 
   handler and guaranteed to be consistent with W3C spec, regardless of which browser you've using.

   If you'd like to use React on touch device such as a phone or tablet, simple call `React.initializeTouchEvents(true);` to enable touch event handing.

> #### Under the Hood: Autobinding and Event Delegation

  Autobinding: When creating callbacks in Javascript, you usually need to explicitly bind a method to its instance such that the value of this is correct. With React,
  every method is automatically bound to its component instance. React caches the bound method such that it's extremely CPU and memory effcient. It's also less typing!

> #### Event delefation: React doesn't actually attach event handlers to the nodes themselves. When  React starts up, it starts listening for all events at the top level
using a single event listener. When a component is mounted or unmounted, the event handlers are simply added or removed from an internal mapping. When an event occurs,
      React knows how to dispatch it using this mapping. When there are no event handlers left in the mapping, React's event handlers are simple no-ops.





































