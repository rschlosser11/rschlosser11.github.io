---
layout: post
title:      "What are Higher Order Components"
date:       2020-09-19 18:16:20 +0000
permalink:  what_are_higher_order_components
---


When I started the Redux section of the curriculum at Flatiron, I learned about higher order components, like connect(), which is used to connect state and dispatch to props of a react component. But what exactly are higher order components?

In order to get a deeper understanding, I reviewed basic Higher Order Functions in Javascript. These are functions that return another function, for example .forEach(), .map(), and .filter() to name a few. An advantage of such functions is reducing bugs, by writing small functions to handle logic that then can be used by a function to perform more complex logic. It seemed reasonable to me that higher order components would do something similar in React.

A Higher Order Component (HOC) is a component that accepts a component as the argument and returns a new component. The component that is returned can render the original component that was passed to it. HOCs are pure functions, meaning they do not mutate the wrapped component in any way.  This could look something like this 

```

import React from 'react';

const higherOrderComponent = (WrappedComponent) => {
   class newComponent extends React.Component {
	   render () {
		   return <WrappedComponent />;
		 }
	 }
}

```

So when can HOCs be used? After doing a bit of research (googling), I discovered there are a many different ways that they can be used, for example, to control whether a component should be displayed or not or to pass through props. This can be done by 

```

import React from 'react';

export default function MyFunction (props) {
    return (
        props.authorized ? <div>You're authorized</div> : <div>You're not authorized</div>
    )
}

```

But this method would mean that every time we want a component to be behind the authorized wall, we'd need to repeat the code and with a more complex components the above method can get a big messy. In order to keep our code DRY and neat, using a HOC can be a good option.

The HOC can look something like this

```

import React from 'react';

const HOC = (Component) => {
    return class Authenticated extends React.Component {
        render() {
            return this.props.authorized ? <Component  /> : <div>You're not authorized</div>
        }
    }
}

export default HOC

```

Note that this.props.authorized is being set in my App.js file, which done very, very simply looks like: 

```

import React from 'react';
import './App.css';
import MyFunction from './myFunction';

class App extends React.Component {
  state = {
    authorized: true,
  }

  render() {
    return (
      <MyFunction authorized={this.state.authorized} />
    );
  }
}

export default App;

```

The component can look like this 

```

import React from 'react';
import HOC from './HOC';

function MyFunction (props) {
    return (
        <div>
            Some super secret information 
        </div>
    )
}

export default HOC(MyFunction)

```

Looking at the HOC above if we had other props to pass through we could add {...this.props} to the component or even go so far as to add or remove props that we want passed to the component. This can be done in the same way as you would do in any other React component. Here's a very simple example

```

import React from 'react';

const HOC = (Component) => {
    return class Authenticated extends React.Component {

        render() {
            const {authorized, secretPlan} = this.props;
            const color = 'blue'
            return authorized ? <Component secretPlan={secretPlan} color={color} /> : <div>You're not authorized</div>
        }
    }
}

export default HOC

```

This supposes that you've updated the App.js file to look like

```

import React from 'react';
import './App.css';
import MyFunction from './myFunction';

class App extends React.Component {
  state = {
    authorized: true,
    secretPlan: 'X marks the spot!'
  }

  render() {
    return (
      <MyFunction {...this.state} />
    );
  }
}

export default App;

```

Finally, I changed the functional component to display the new props

```

import React from 'react';
import HOC from './HOC';

function MyFunction (props) {
    console.log(props)
    return (
        <div>
            Some super secret information: <br />
            Plan: {props.secretPlan} <br />
            Color: {props.color}
        </div>
    )
}
export default HOC(MyFunction)

```

This very simply would return:

Some super secret information:
Plan: X marks the spot!
Color: blue

As I read more and more about HOCs, I realized the immense value they can add to a project and I am looking forward to learning more about them and using them to keep my coding DRY and easy to read.

