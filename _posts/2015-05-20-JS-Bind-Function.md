---
layout: post
title: Javascript Bind Function.
---

### JavaScript's Bind Method

We use the `Bind()` method primarily to call a function with `this` value set explicitly. In other words, `bind()` allows us to easily set which specific object will be
bound to this when a function or method is invoked.

This might seem relatively trival, but often the `this` value in methods and functions must be set wxplicitly when you need a specific object bound to the function's this
value.

The need for `bind` usually occurs when we use the `this` keywords in a methods and we call that method from a receiver object; in such cases, sometimes `this` is not 
bound to the object that we expect it to be bound to, resulting in errors in our applications. Don't worry if you don't fully comprehend the preceding sentence. It will 
become clear like teardrop in  a moment.

Before we look at the code for this section, we should understand `this` keyword in JavaScript. If you don't understand well, you will have trouble understanding some of
concepts discussed below. In fact, many of the concepts regarding setting the `this` value that I discuss in this article I also discussed in Understand JavaScript's this.


### `Bind()` allow us to borrow methods

In Javascript, we can pass functions around, return them, brorrow them, and the like. And the bind() method makes it super easy to borrow methods
