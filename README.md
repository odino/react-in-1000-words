# React in 1000 words

The goal of this guide is to be able to familiarize with ReactJS.
You'd probably have a better time here by being already familiar with
JS and ES6, but I wrote this small guide to be easily digested by anyone
who has a bit of programming background. I won't go into the details of what
JSX is, what id the virtual DOM and so on as these are left to the reader: the
main idea behind this tutorial is to be able to understand React's ideas and
functionalities without having to take a deep dive.

Let's get started!

## Intro

React is a library aimed to ease building user interfaces: think of
it as an easy way to build an HTML view with clicks and interactions.

The core of React starts with components. Components are, in a nutshell,
just plain classes:

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

To tell React to include our component in the page, we simply have to bind it
to an HTML element:

``` html
<html>
  <body>
    <div id="container" />

    <script src="//cdn.com/react.js" />
    <script type="text/javascript">
      ReactDOM.render(
        <LoginForm />,
        document.getElementById('container')
      );
    </script>
  </body>
</html>
```

We've now seen how to write the simplest possible component, and how to
"bind" it to the DOM.

## Interactions

In order to add some interaction to our component, we need to introduce
a concept called **state**: React components have some state that's used
to keep track of what's happening within the component.

For example, we could add some silly validation on our login form, by making
sure the user enters a password longer than 5 characters. To do so, React
asks us to initialize our component with some "known" state:

``` jsx
class LoginForm extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      password: ''
    }
  }

  render() {
    // ...
  }
}
```

Now, don't worry about understanding what those `props` are -- we'll get back at
it later on. For now the most important thing is to know that if we want to
manage state in our component we should initialize a `state` property in the
component and assign values there. If we want to change the state, we can use
`this.setState({...})` and react will overwrite the existing state.

We can then use the state in our render function:

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
thus updating the UI. This is a very important concept in React: **when state
changes, the component re-renders**. But hey, you can't just do `this.state.password = e.target.value`,
as React forces you to use the `setState` method, so that it can "see" that the
state has changed: if you were to update the state "directly", React would have
no visibility of the change.

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
      <form onSubmit={e => this.onSubmit(e)}>
        <input type="text" />
        <input type="text" value={this.state.password} onChange={e => this.setState({password: e.target.value})} />
        <input type="submit" />
      </form>
    )
  }
}
```

As you see, you can define interactions with inline functions:

```
onChange={e => this.setState({password: e.target.value})}
```

as well as component methods:

```
onSubmit={e => this.onSubmit(e)}
```

The choice is really yours: the rule of thumb is that when the function is very
small, you can "inline" it, else it's better to keep it "separate" for
readability.

## Components altogether

Now, what would be the use of apps with a single component? At the end of the
day, you will want to integrate multiple components together to build your UI.

Let's build a simple UI that presents 2 LoginForm components, one for the
user and one for the admin (this is a pretty useless feature, I know, but will
help  us understand a few more things):

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
simply "embed" them as HTML / XML tags.

Ok so, we have [2 forms](https://jsfiddle.net/0cv3dgko/) now!

Now, you remember how I told you earlier to forget about those "props"? Let's
get back at them!

Say that we wan't to make sure the UI tells the user the forms are for different
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

https://jsfiddle.net/ycbe2vrs/

Let's say the props are something we inherit from our parent components, whereas
the state is something that belongs to the component only.
As I said earlier, changing the state triggers s re-render of our component.

React is also smart enough to trigger a re-render when the props change, as it
means that something has changed in our parent component:

``` jsx
class MyView extends React.Component {
	constructor(props) {
    super(props)

    this.state = {
      adminName: "admin"
    }

    setTimeout(_ => {
    	this.setState({adminName: 'chtulu'})
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

As simple as that. You are now equipped with enough to start writing your first
React app (don't worry, since it's the first it'll be crappy...and that's ok!).

## Lifecycle methods

## Not only the web
