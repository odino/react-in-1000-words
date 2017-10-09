# React in 1000 words

The goal of this guide is to be able to familiarize with ReactJS.

You'd probably have a better time here by being already familiar with
JS and ES6, but I wrote this to be easily digested by anyone
who has a bit of programming background.

## Intro

**React is a library for building user interfaces**: think of
it as an easy way to build a view with clicks and interactions.

The core of React starts with components, which are plain classes:

``` jsx
class LoginForm extends React.Component {
  render() {
    return (
      <form>
        <input type="text" />
        <input type="password" />
        <input type="submit" />
      </form>
    )
  }
}
```

When React runs, it will look for a `render` method in your component and
lay the HTML out based on what tags are returned by the method: in this case,
we're going to be creating a form with 2 text inputs and a submit button.

## Interactions

In order to add some interaction to our component, we need to introduce
a concept called **state**: React components have some state that's used
to keep track of what's happening in the UI.

Let's add some silly validation on our login form, by making
sure the user enters a password longer than 5 characters. To do so, React
asks us to initialize our component with some initial state:

``` jsx
class LoginForm extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      password: ''
    }
  }

  ...
}
```

Now, don't worry about understanding what those `props` are -- we'll get back at
them later on. For now the most important thing is to know that if we want to
manage state in our component we should initialize a `state` property in the
component's constructor and assign values there.

If we want to change the state, we can use `this.setState({...})` and React will
patch the existing state:

``` jsx
class LoginForm extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      password: ''
    }
  }

  render() {    
    return (
      <form>
        <input type="text" />
        <input type="text" value={this.state.password} onChange={e => this.setState({password: e.target.value})} />
        <input type="submit" />
      </form>
    )
  }
}
```

As you see, we're now binding the value of the password field to `this.state.password`
and, whenever the user types on the input, we update the state.

When the state changes, React is smart enough to call the `render` method again,
thus updating the UI (try typing in [this fiddle](https://jsfiddle.net/rbvrkhxz/)).
This is a very important concept in React: **when state
changes, the component re-renders**.

Note you can't just do `this.state.password = e.target.value`,
as React forces you to use the `setState` method so that it can "see" that the
state has changed: if you were to update the state directly, React would have
no visibility on the change.

Let's "finalize" our example by handling the submit action:

``` jsx
class LoginForm extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      password: ''
    }
  }

  onSubmit(e) {
    if (this.state.password.length < 5) {
      e.preventDefault()
      alert('oooops')
    }

    // Happy path code...
  }

  render() {    
    return (
      <form onSubmit={this.onSubmit}>
        <input type="text" />
        <input type="text" value={this.state.password} onChange={e => this.setState({password: e.target.value})} />
        <input type="submit" />
      </form>
    )
  }
}
```

As you see, you can define interactions with inline functions:

``` jsx
onChange={e => this.setState({password: e.target.value})}
```

as well as component methods:

``` jsx
onSubmit={this.onSubmit}
```

The differences between the 2 are subtle so don't worry about them now.

## Components altogether

At the end of the day, you will want to integrate multiple components together
to build your UI, so let's build a simple view that presents 2 login forms, one
for regular users and one for the admin:

``` jsx
class MyView extends React.Component {
  render() {
    return (
      <div>
        <LoginForm />
        <LoginForm />
      </div>
    )
  }
}
```

Ouch, that was easy! In order to include components in other components you can
simply "embed" them as HTML / XML tags: we have [2 forms](https://jsfiddle.net/0cv3dgko/) now!

Now, you remember how I told you earlier to forget about those "props"? Let's
get back to them!

Say that we want to make sure the UI clearly states the forms are for different
users, we can "tag" our components with different attributes:

``` jsx
class MyView extends React.Component {
  render() {
    return (
      <div>
        <LoginForm role="regular" />
        <LoginForm role="admin" />
      </div>
    )
  }
}
```

and in our `LoginForm` we can access these "tags" via the `props`, which is a
shortcut for "component's properties", or "properties", or "props":

``` jsx
class LoginForm extends React.Component {
  constructor(props) {
    super(props)

    ...
  }

  render() {    
    return (
      <form onSubmit={e => this.onSubmit(e)}>
        <div>
          This is the form for the {this.props.role} user
        </div>

        ...
      </form>
    )
  }
}
```

[Et voila!](https://jsfiddle.net/ycbe2vrs/)

Let's say the **props are something we inherit from our parent component**
(some sort of external initialization parameters), whereas
the **state is something that belongs to the component only**.

As I said earlier, changing the state triggers a re-render of our component, but
React is smart enough to trigger a re-render when the props change as well:

``` jsx
class MyView extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      adminName: "admin"
    }

    setTimeout(_ => {
    	this.setState({adminName: 'cthulhu'})
    }, 1000)
  }

  render() {
    return (
      <div>
        <LoginForm role="regular" />
        <LoginForm role={this.state.adminName} />
      </div>
    )
  }
}
```

As simple as [that](https://jsfiddle.net/r5p942h2/)!

## Lifecycle methods

As we saw, React takes advantage of your components implementing some "common"
method, like `render`. There are quite a few more methods you can implement
in your components that will be used by React -- let's call them [lifecycle
methods](https://engineering.musefind.com/react-lifecycle-methods-how-and-when-to-use-them-2111a1b692b1)
since they run at different stages of the component's lifecycle.

The only method I want to mention here is `shouldComponentUpdate(nextProps, nextState)`,
which allows you to decide whether the component should trigger a re-render when
it receives new props (the parent changes) or new state (via `this.setState({...})`):

``` jsx
shouldComponentUpdate(nextProps, nextState) {
  if (this.state.adminName === nextState.adminName) {
    alert('avoided a re-render')
    return false
  }

  return true
}
```

You can see it in action [here](https://jsfiddle.net/2fyrw3hq/).

Why did I decide to mention it? Using `shouldComponentUpdate` effectively
translates in good performance optimizations, since you can avoid re-rendering
your components. More on this in the links at the bottom of this guide.

You are now equipped with enough to start writing your first
React app: let me just add a couple more paragraphs to clarify some things.

## Not only the web

I'd like to stress on the fact that, in my opinion,
 **React is an approach more than a library**: the idea is that you have
re-usable components that a library (React) renders and manages, while you focus
on how your components live & interact with each other.

Our examples run on the browser, but the React team has been working on renderers
for [mobile applications](https://facebook.github.io/react-native/docs/getting-started.html)
and [VR browsers](https://facebook.github.io/react-vr/) among others.

At the end of the day what changes is how to render what's returned by the `render` method,
but everything else (the component's lifecycle & inner workings) stays the same
across platforms.

## ...what now?

We barely scratched the surface, but you should be good to go and build your
first React app. A few pointers for the curious ones:

* a [primer on virtual DOM](https://www.codecademy.com/articles/react-virtual-dom), which is a great performance optimization React popularized
* what's this weird XML in the `render` function? Meet [JSX](https://jsx.github.io/)!
* [React's top-level API](https://reactjs.org/docs/react-api.html)
* how to embed a component **inside** another component tag? Meet [this.props.children](https://learn.co/lessons/react-this-props-children)!
* re-rendering is sometimes expensive, so you should consider using [pure components](https://60devs.com/pure-component-in-react.html) and [immutable objects](https://github.com/facebook/immutable-js/wiki/Immutable-as-React-state)


```
~/projects/react-in-1000-words$ pandoc README.md | lynx -stdin -dump | wc -w
1257
```

Ok, I cheated a bit...
