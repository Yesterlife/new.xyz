---
layout: post
title: Why you not need MVC with React.
---

I've compared React to tradional MVC in this blogpost.

I covered one important point: re-rending everything is nice. But I also claimed all kinds of other things (like not needing MVC patterns), but didn't actually reason
about them. This cause me to write this follow up.

Let me throw out disclaimer:

I will question the usefulness of MVC (MVC as todays MV* implementations in JS frontend frameworks), the most established design patterns in frontend development. Let
    me make it clear that it is not my intention to invalidate MVC in general. Rather, I think it's importantance as a design pattern for very interactive web applications is overstated.

This post here is ballsy, since MVC is baked into the heads of frontend people, it's ubiquitous. It's the biggest consensus amongst devs I've ever seen, and MVC is widely considered "best practice". Basically, people don't question MVC anymore. However, I personally think the concept doesn't apply opatimally to complex web apps (I might be wrong though).

##### When does simple MVC go sideways ?

For the sake of the argument, let's consider the base of todays MVC landscape, Backbone.js, and establish when things get tough:

1.Stage 1: Unproblematic
    Let's assume you want to display a large list of data items in your app. This is easy to do with backbone. You can even use the Backbone Router to make a lot of such lists available, so this use case scales well. This is where Backbone shines.

2.Stage 2: Still managable
    Let's say you want to make your app a little more interactive. You add some visiblity toggles for certain DOM elements on the view that displays your data items or maybe you'd like to add a simple filter for the data items. Depending on the exact scenario, this ranges between easy and still managable.

3.State 3: Dange zone
    You want to implement multiple additive filters for your list, and additionally make this list explorable for the user by letting him choose how the list is grouped and stored. You will find yourself wanting to make things like nested views, and you will implement change listeners all over the place to rerender specific views. It's just a matter of quantity until everything blows up in your face.

The trouble maker.

The thing that makes this stuff hard is state. This is not surprising, state is your enemy in almost every programming problem. MVC helps you to manage state. But only specific type of state, namely data state. Loading data from the server and visualize it in a view is easy with Backbone. You can display as much as data as you want without creating a mess.

Let's contract this with a type of state that I call application state: The application state defines what data is displayed in what way at the moment. Backbone driven solutions usually turn sour because a backbone app can only handle so much application state until things become messy. In other words: The more interactivity, the harder it gets. The optional case is when application state is only represented by tge URL (state 1). As soon as there is interaction between different views (state 3), things will get harder by the minute.

Other MVC frameworkd offer solutions for these problem. However something bugs me about this:

The solutions to thest problems have nothing to do with MVC as a design pattern. You can detach the model from the view all you want, you can call your controlling abstraction thingy whatever you want (controller, presenter, whatever), the problems won't go away until you introduce new concepts to keep order.

#### The missing thing in MVC

Here is what MVC doesn't cover: It does not define how communication flows. If your application is very stateful in terms of application state(read: interactive), the user can trigger changes to state all over the place. To not go insane, there needs to be a defined flow of how the information about state changes travels between components, so we can orient ourselves everywhere in the app.

For example: if you click a button to hide a div, it needs to be defined on what path the state change travels to the object that actually hides the div, inrespective of where the button is.

Think of an electrical grid or closer to our domain, a network. There is a defined, ordered flow in these types of system. Not every computer attached to the internet has a direct connection to every other computer, they are connected indirectly through nodes. Only this way they become understandable and therefore manageable.

With Backbone, you have the global event system, and you are very tempted to pull wires all over the place. This is fine if you have just a few wires, but boy are you in trouble if you have many. The responsible dev invents a specific flow that provides structure, but there is always the possibility that after a big change, it doesn't work out anymore, which results in a rewrite of the whole system.

You see, with a naive MVC pattern, information flow within a view is easy, since it's singel, flat object. But butween views, the information flow is unmodeled and therefore hard.

You might argue that this is the job of the controller. However, in a complex app, these controlling duties can not be done with a signle controller object. You need a control system and a control system is defined by many nodes and ordered communication flow between them.

#### information flow architecture: The obvious choice

The bigger MVC frameworks do a lot of work to orchestrate this data flow. But why reinvent the wheel? There is already a blueprint for a data flow layout available for free: The DOM.

Events already use it. They bubble up and down through nodes. The so called Cascading Style Sheets use it well.

The DOM has a tree structure that defines relations between display delements, Accdientally, communication systems are usually also modeled like a tree.

So why not use the DOM hierarchy as the layout for the communication aechitecture? It aligns prefectly with the DOM event system. It aligns with the way CSS works.

This is exactly what React.js does. You define components and put them into parent-children relations to describe the DOM. Additionally the parents pass down the necessary state to the children, and if needed, the channel to change the state. This automatically builds communication channels in a tree structure that naturally represents the reasoning of you app.

I cannot overstate how awesome this idea is. It took me a short time to get it and as soon as I got it, I felt like there is no problem that cannot be modeled decently with this system. Even better, it is equal to how DOM events work. It becomes very natural to pick up a DOM event and transform it into state. The best part: It scale almost indefinitely!

To use an analogy, instead of having a limp puppet that is controlled via strings by the puppet master (a controller controlling a view in MVC), you make communication channels that endogenrously go through the whole system, like nerve fibers in the human body. There is always a clear structure on how information travels: ALong the limbs through nerve fibers from and to the root node, the brain.

Using the DOM as a communication layout means that if your DOM tree is well-structured, your communication system is also well-structured! I bet a lot of apps have a well-structured DOM right now, but a bad communicaiton layout.

Communication along the DOM nodes is at the absolute center of React. The fun thing is that once communication between components is fully taken care of, there is not much else to solve (usually). So most of the time, there is no need for an MVC pattern anymore because all the controller and most of the views resonsibilities have to do with communicaition of state anyways, and they are already taken care of.

#### How to model state.

Communication is one part of managing state. But there is more to it. State needs to be explicit, and React does this rigorously. With todays MVC frameworks, there is a natural tendency to store application state implicitly in the DOM, and everything that is implicit needs to be forgotten and duplicated if needed in more than one place. This is bad.

Additionally, state is easier to reason about if its implementation is hidden, it should not matter if state is implemented with a model or via the URL. Everybody that once tried to move state from the route to somewhere else in a Backbone driven app knows how much work this can be because the implementation of the state is not hidden.

#### Easy != simple
    
There are probably people that will argue that there is frameworks X that provides solutions for all of these problems just as well. Agreed, they do. But there is one big difference: You need to learn a lot more stuff to use them.

An experienced Ember.js dev that knows Ember in and out will say that he finds it easy to mamange application state in EMber. I cannot disagreen since easiness is subjective, but what were the costs to achieve this easiness? Almost all sources mention the steep learning curve of the bigger MVC frameworks. You need to know a lot of stuff and spent a lot of time to learn all their concepts.

In contract, everything you need to read to "get" React conceptually is this. It's one small example. React is an extramely simple concept because it just describes the DOM with conponents, and components communicate along the COM structure.

I believe that simplicity should be the ultimate goal in a software solution. Somple concepts become easy quicker than complex ones. If you have a concept that is teice as simple as another one, but solves the problem with the same case, then why choose the complex one ?

#### Conclusion

If you have a lot of data to diaplay, but not that much other state in your applicaiton, then MVC is a great concept to organize your app. When you have a lot of interaction and a lot of application state, the problems that come with that state dominate the problems MVC solves.

communication between componentsabout changed state cause the biggest headaches, and because React centers around the solution to that problem, writing stateful apps is much simpler in React. You can see this in the dace that the "Hello World" or the ToDoMVC examples don;t look that nice in React, but a complex, interactive app looks awesome.

In a React app, MVC patterns are not in the center of attention anymore, and might not event be present at all. 

I only realized clearly what it is that makes interactive apps hard after React provided me with the solution. I can only urge you to try and embrace it the next time you write something interactive. You will learn a lot about frontend development.

