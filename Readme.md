# Intro to React Native

## Learning Objectives

- Explain the problem React Native solves and its approach to solving it
- Build an iOS app with React Native
- Utilize `Text`, `View`, `ListView`, `Image`, and `TextInput` components to construct native screens
- Explain how to style components in a React Native application

## Framing

React Native is a framework for building cross-platform apps.

React Native is like React, but it uses native components instead of web components as building blocks. In its approach to building cross-platform apps, React Native does not aim to be a cross platform, write-once run-anywhere, tool. But rather it aims exemplify a learn-once write-anywhere paradigm. An important distinction to make. This lesson only covers iOS development, but once you’ve learned the concepts here you could port that knowledge into creating an Android app very quickly.

**Q**: What do we mean by native components?

At a high level, it’s important distinction to make that we are no longer on the (desktop) web. This means different UX, different UI, and no URL. The thought processes we've established around building UIs for the web will help, but won’t be enough to create a great experience on mobile.

## Getting Started

React Native uses [Node.js](https://nodejs.org/), a JavaScript runtime to build our JavaScript code, as well as requires several dependencies in order to run. We can install these with `brew`.

### Installing dependencies

First, we need to use `homebrew` to install [watchman](https://facebook.github.io/watchman/), a file watcher from Facebook:

```bash
$ brew update
$ brew install watchman
```

This is used by React Native to figure out when your code changes and rebuild accordingly.

Next up, we need to get the React Native CLI tools, which will allow us to initialize and run our applications.

Before we start running an iOS app, as recommended let's use Facebook's JS package manager [yarn](https://yarnpkg.com/en/docs/install) to install the tools locally:

```bash
$ brew install yarn
$ yarn global add react-native-cli
```

The only thing left is to set up our build tool that will simulate the iOS environment. One of the most popular options if you are on OS X  for this process is Apple's `XCode` application which you can install from the App Store.

> **Note**: the process may take 1 to 2 hours for this download to complete, make sure to connect your computer to a power source and do not interrupt the process. If you want to skip to writing code now without setting up the environment, we recommend checking out [Facebook's interactive tutorial](https://facebook.github.io/react-native/docs/tutorial.html#hello-world)

### React-Native Init

Now that we have our dependencies, let's use the react-native-cli tool to generate a new project for us.

```bash
$ react-native init ContactApp
$ cd ContactApp
```

Included in the project is some starter code for both iOS and Android platforms. Today, we will focus on developing an iOS app, so let's start by examining the contents of `index.iOS.js`

```jsx
// Using ES6 Modules to import dependencies - namely React and some components from React Native
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

// Definition for our app root component
export default class ContactApp extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.iOS.js
        </Text>
        <Text style={styles.instructions}>
          Press Cmd+R to reload,{'\n'}
          Cmd+D or shake for dev menu
        </Text>
      </View>
    );
  }
}

// This is how we style components in React Native - with JS!
const styles = StyleSheet.create({
  container: {
    flex: 1,s
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

// Bootstrap our app's root component so it will load, initialize, and render our app
AppRegistry.registerComponent('ContactApp', () => ContactApp);
```

We are looking at the entry point to the Sample App that `react-native-cli` creates when we initialized our project. The general syntax should look familiar from the React components we wrote for the DOM, but let's go ahead and explore how to write components in our new environment.

### Imports and Building Blocks

At the top of the file, we see that we are loading in the `React` library and the`Component` base class from `React` just like before, but then we are also importing some predefined methods and components from the `React-Native` library:

> [**AppRegistery**](https://facebook.github.io/react-native/docs/appregistry.html) - is the JS entry point to running all React Native apps. App root components should register themselves with `AppRegistry.registerComponent`, then the native system can load the bundle for the app

> [**View**](https://facebook.github.io/react-native/docs/view.html) - the most fundamental component for building a UI, `View` is a container that supports compossible layout, meaning a`View` is designed to be nested inside other views and can have 0 to many children of any type.

> [**Text**](https://facebook.github.io/react-native/docs/text.html) - a React component for displaying text.

> [**StyleSheet**](https://facebook.github.io/react-native/docs/stylesheet.html) -  is an abstraction similar to CSS StyleSheets. Using StyleSheet to create style objects to be used inline by components leads to higher performance and code quality.

These are the fundamental building blocks used to construct our own components, and represent the parts
of the React Native that we need to quickly bootstrap a React Native application.

Now to compile our application and view it in the simulator, we can run:

```bash
$ react-native run-iOS
```

This should build our app and open it up in an iOS simulator powered by XCode.

## Building a Contact List App

To put our React skills to the test in this new environment, we'll start by building an application to help manage some contacts. This app will push us to use many of the APIs React Native provides for doing common tasks such as collecting user input, rendering lists of data, and updating the UI based on state changes.

### `ContactApp` Component

Let's begin by looking at a quick mockup of what we are going for:

![Contact-App-Mockup](https://s21.postimg.org/b0n1x0vlz/contact_App.png)

Our Contacts app can be broken down into two main components: an `ContactApp` container component, a `ContactList` list component. All of our app's state will live in the container component, and  the `ContactList` component will be broken down into multiple presentational components, namely `Row`, and `Search`.

To start, let's get rid of the generated code in our app's root component, and define our basic structure and state.

### [Structuring State and Component Outline](https://github.com/ga-wdi-exercises/react-native-contacts/commit/34fc7ae5e81ce6a9f0c6f39c570a55ad0aa29288)

Before we start building our application, with the design of our components in mind - let's try to brainstorm all of the important changing data we will need to track in our users' flow.

#### Turn and Talk

- What pieces of state should we keep track of as we build our application?
- What will be the way primary ways we will update our app's state?

---

Since our application will be focused on contacts, we will need to store information about all contacts and the user search term for filtering contacts. Let's go ahead and map the pieces of data we will need to track by initializing some default state for our `ContactApp`:

```jsx
// import code omitted above

export default class ContactApp extends Component {
  constructor(props) {
    super(props)
    this.state = {
      contacts: [],
      searchTerm: '',
    }
  }

  render() {
    return (
    	<View>
        <ContactList>
          <ContactSearch />
          <Row />
        </ContactList>
      </View>
    )
  }
}

AppRegistery.registerComponent( 'ContactApp', () => ContactApp )
```

> **Note**: In our render function, we are rendering our a`ContactList` component which itself has several children components. While we have yet to define these yet, we will do so momentarily.

Great, now that we have a good idea of the structure and data of our application, let's get to work building out the rest of our components with some actual data.

### Using `ListView` to Render Scrollable Lists

For our dataset, we've taken advantage of the great free service [Random User Generator](https://randomuser.me/) to create some fake user data that [we've included](./contactsData.json) for our use. The JSON seed data is structured as an array of objects containing metadata such as `firstName`, `lastName`, `email`, `phoneNumber`, and `imageUrl` for each fake contact.

First, we need to create a file in our project's root directory to hold all of our data, let's call that file `contactsData.json`:

```bash
$ touch contactsData.json
```

Then, we can go ahead and copy the contents of the [seed data](./contactsData.json) and place it into that file.

In order to render this data, we are going to use the pre-built `ListView` React Native component, which specializes in displaying dynamic scrollable data. To use it, we need to first import the component from React Native, and import our seed data.

In `index.iOS.js`:

```js
import React, {Component} from 'react'
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  ListView
} from 'react-native'
import ContactsData from './contactsData'
```

Great, now that we have those pieces in place, let's define our `ContactList` component which we will use to render a `ListView` and which will receive the correct data as props.

### [`ContactList` Component](https://github.com/ga-wdi-exercises/react-native-contacts/commit/4f40cf5e39a14c134ec31644d06105d90e663430)

Let's put our new component definition after our imports, and before our `ContactApp` component definition:

```jsx
// ContactList component
class ContactList extends Component {
  render() {
    return (
      <ListView
        dataSource={this.props.dataSource}
      {/* data represents each item in the array passed as the dataSource - in this each contact  */}
        renderRow={data => <Text>{data.firstName}</Text>}
        />
    )
  }
}
```

> **Note**: all `ListView`'s must have a `renderRow()`method defined as a prop, which is in charge of the rendering of each item in the passed in dataset. Right now, we are simply rendering each contact's first name as text inline, but in the future we will break this out into its own component so to have more control over the display.

 As you can see, we need to satisfy the value for the prop `dataSource` which is currently coming from `this.props.dataSource`. Let's actually pass this component some data, when we render it in our `ContactApp`.

#### Setting a Data Source

Our `ContactApp` component is in charge of all our app's state and rendering our other components. Let's go in, and update that component's definition so that it will pass the correct data to the `ContactList`.

First, when our app is initialized we need to use `ListView.DataSource()` to configure how the data is structured, and specifically, to define how to differentiates between rows:

```jsx
// index.iOS.js

export default class ContactApp extends Component {
  constructor(props) {
    super(props)
    // initialize a dataSource where a row is defined to be different from the previous one
    const ds = new ListView.DataSource({ rowHasChanged: (row1, row2) => row1 !== row2 })
    this.state = {
      contacts: contactsData, // update state to pull from the seed data
      dataSource: ds.cloneWithRows(contactsData), // need to format our seed data correctly
      searchTerm: '',
    }
  }

  render() {
    return (
    	<View>
          {/* Pass the updated dataSource as a prop */}
          <ContactList dataSource={this.state.dataSource} />
      	</View>
    )
  }
}
```

> [`ListView.DataSource`](https://github.com/facebook/react-native/blob/master/Libraries/CustomComponents/ListView/ListViewDataSource.js) does a lot of things behind the scenes that allow us to have an efficient data blob that the `ListView` component can understand, all from a simple array. Also important to note, we need to treat the data we pass as a dataSource as **immutable**.

If we now reload in the simulator, we should see all of our contacts' first names rendered on the screen! Next up, let's work on adding some styles and a little more markup to each `Row` in the list.

---

## Break

---

### (You - Do) [Refactoring To Use A Row Component](https://github.com/ga-wdi-exercises/react-native-contacts/commit/bc8e8f2429784ecba4eb39a12cb52cf51d950bab)

Now that we have our basic app structure in place, it's a good time to take the opportunity to refactor and add some styles to our rendered data.

Specifically, let's take that little bit of rendered UI from the `renderRow()` method in our `ContactList` component, and break it out into its own `Row` component that we will render instead. This will leave our `renderRow()`looking something like this we we are done:

```jsx
// ContactList
<ListView
  dataSource={this.props.dataSource}
  renderRow={data => <Row contact={data} />} />
```

**Your task is to define our `Row` component**

You should:

- Render your new `Row` component in the `renderRow()` method in the `ListView`
  - Pass in the data as a `contact` prop
- Render a `View` containing the contact 's profile picture with an `Image` component
- Render the full name of the contact in a `Text`

#### Adding Styles with React Native

Once you have your initial markup, try adding some inline styles to your `Row` Component:

- Use `Stylesheet.create()` to create a `styles` object with computed javascript `CSS` rules defined
- Define `rowContainer`, `rowPhoto`, and `rowText` rules and apply them to their respective bit of UI in the `Row` render method

Ultimately in the mobile landscape, the [React Native flow of styling](https://facebook.github.io/react-native/docs/style.html) leads to a more modular, targeted approach to styling components, rather than creating traditional external stylesheets. This allows for styles to be easily reused, and only applied on components as they are rendered down the chain.

#### [Adding A Row Separator](https://github.com/ga-wdi-exercises/react-native-contacts/commit/08bd991ba339331209b00e00f50b59664e3a1a15)

Now that our UI for our initial view is starting to come together, let's add a nice visual separator between our rendered rows. Luckily, the `ListView` component has a `renderSeparator()` method that can help us display a separator between components in our list.

To do this, we need to define a new style and add tiny bit of UI:

```jsx
class ContactList extends Component {
  render() {
    return (
      <ListView
        dataSource={this.props.dataSource}
        renderRow={data => <Row contact={data} />}
        renderSeparator={(sectionId, rowId) => <View key={rowId} style={styles.separator} />}
       />
    )
  }
}

/* ... */

const styles = {
  /* ... */
  separator: {
    flex: 1,
    height: StyleSheet.hairlineWidth,
    backgroundColor: '#8E8E8E',
  }
}
```

> **Note**: the `rowId` is passed as a prop in to the `renderSeparator` method, which works well as a key 
>
> The `StyleSheet.hairlineWidth` property is defined as [the width of a thin line](https://facebook.github.io/react-native/docs/stylesheet.html#hairlinewidth) on the platform. It can be used as the thickness of a border or division between two elements.

Now, you might be asking why not just tack on a border bottom on the component returned by *renderRow*? One main reason is that *renderSeparator* is smart! It won’t render the separator on the last element in the section. 

### [Adding Search UI to Filter Contacts](https://github.com/ga-wdi-exercises/react-native-contacts/commit/e5b041c9ced96dac9042189ae709199e297ad2d1)

Let's continue implementing our outlined features and add a way for users to filter our contacts by name.

We'll start by adding a new component `ContactSearch`, which we will render as the header in the `ListView`. To do this, we'll utilize the `renderHeader()` method, following a similar pattern we've been doing so far.

However, before we can begin writing our new component, we need a way to collect user input, and thus we'll have to import another native component, `TextInput`. This will allow us to track changes to the input, and update our app's state accordingly.

With our data in mind, the `ContactSearch` component will receive the `searchTerm` and necessary event handlers as `props` that we will define and pass down in our `ContactApp` container component.

#### Adding UI

First up, let's focus on creating the necessary UI before we worry about wiring up the input to the app's state:

```jsx
/* after our imports but before our ContactApp definition */

// ContactSearch component
const ContactSearch = props => {
  return (
    <View style={styles.searchContainer}>
      <TextInput
        style={styles.input}
        placeholder="Type to Filter..."
      />
    </View>
  )
}
```

Now we just need to define the styles we're referencing and then render it in the `ContactList` component via the `renderHeader()` method:

```jsx
// ContactList component
class ContactList extends Component {
  render() {
    return (
      <ListView
        dataSource={this.props.dataSource}
        renderRow={data => <Row contact={data} />}
        renderSeparator={(sectionId, rowId) => <View key={rowId} style={styles.separator} />}
        renderHeader={() => <ContactSearch />}
       />
    )
  }
}

/* ... */

const styles = {
  /* ... */
  searchContainer: {
   flex: 1,
   padding: 8,
   flexDirection: 'row',
   alignItems: 'center',
   backgroundColor: '#C1C1C1',
  },
  input: {
   height: 30,
   flex: 1,
   paddingHorizontal: 8,
   fontSize: 15,
   backgroundColor: '#FFFFFF',
   borderRadius: 2,
  },
}
```

If we refresh our app in the simulator, we should see our search UI added to the top of the screen, but it doesn't do any thing...yet!

Let's use some state to track user input and update our app accordingly.

#### Filtering Contacts

In order to wire up our `ContactSearch`, we will need to pass down some data and handlers from the container component.

If you remember from when we defined our app's initial state, we already have a piece of state in the `ContactApp` we've called `searchTerm`.  We will pass that data and an `onSearchInput` handler down the chain from `ContactApp` to `ContactList` to `ContactSearch` via props:

```jsx
// ContactSearch component
const ContactSearch = props => {
  // grab the searchTerm and handler from props
  let { searchTerm, onSearchInput } = this.props
  return (
    <View style={styles.searchContainer}>
      <TextInput
        value={searchTerm}
        onChangeText={ text => onSearchInput(text) }
        style={styles.input}
        placeholder="Type to Filter..."
      />
    </View>
  )
}

// ContactList component
class ContactList extends Component {
  render() {
    return (
      <ListView
        dataSource={this.props.dataSource}
        renderRow={data => <Row contact={data} />}
        renderSeparator={(sectionId, rowId) => <View key={rowId} style={styles.separator} />}
        renderHeader={() => (
          <ContactSearch
            searchTerm={this.props.searchTerm}
            onSearchInput={this.props.onSearchInput}
          />
        )}
       />
    )
  }
}

// ContactApp component
export default class ContactApp extends Component {
  constructor(props){

    /* ... */

    // bind method to instance
    this.handleSearchInput = this.handleSearchInput.bind(this)
  }

  handleSearchInput(searchTerm) {
    console.log(searchTerm)
  }

  render() {
    return (
      <View style={{top: 18}}>
        <ContactList
          dataSource={this.state.dataSource}
          onSearchInput={this.handleSearchInput}
          searchTerm={this.state.searchTerm}
        />
      </View>
    )
  }
}
```

> **Note**: we added an inline style prop `style={{top:18}}` to the ContactApp's container to help with the list header's display.

Ok, so it might seem like there's a lot going on in this step, but this is the same pattern we saw in React where we are passing state down between components as props, and letting the container component be in charge of responding to user input. When we refresh our app in the simulator and if we have `remote debugging` enabled, we should be able to see the user's input logged to our console in the debugger UI tab.

This is a great sign, that we can proceed to use that input to perform the necessary filtering. All that's left is to update our `handleSearchInput` method in the `ContactApp` and write the logic to filter by a contact's full name:

```jsx
// ContactApp component
export default class ContactApp extends Component {
  constructor(props){
    super(props)
    const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2})
    this.state = {
      contacts: contactsData,
      dataSource: ds.cloneWithRows(contactsData),
      searchTerm: '',
    }

    // bind method to instance
    this.handleSearchInput = this.handleSearchInput.bind(this)
  }

  handleSearchInput(searchTerm) {
    // given a contact, see if the search term in that contact's full name
    let filterbyFullName = contact => {
      let fullName = `${contact.firstName} ${contact.lastName}`
      return fullName.includes(searchTerm.toLowerCase())
    }

    // filter our list of contacts, making sure to preserve immutability
    let updatedContacts = this.state.contacts.slice().filter(filterbyFullName)

    // if there's no search results, return a copy of original dataset
    updatedContacts = updatedContacts.length ? updatedContacts : this.state.contacts.slice()

    this.setState({
      ...this.state, // returns a copy of the previous state
      dataSource: this.state.dataSource.cloneWithRows(updatedContacts), // update the dataSource
      searchTerm, // update the searchTerm controlling the input
    })
  }

  render() {
    return (
      <View style={{top: 18}}>
        <ContactList
          dataSource={this.state.dataSource}
          onSearchInput={this.handleSearchInput}
          searchTerm={this.state.searchTerm}
        />
      </View>
    )
  }
}
```

In the `handleSearchInput` method, we filter our contacts by the full name based on the search term, and then we call `this.setState` to update the `dataSource` property which is in charge of what is rendered by our `ListView`. Important to note, during this step we make use of the nature of `.slice()` to ensure that we don't mutate our data source directly.

After all that, if we go reload in the simulator now, we should be able to search for a contact by either first or last name, and watch as the list dynamically renders the results!

## Closing: What's Next?

Now that we know the building blocks of React Native, we can build some pretty cool apps that will render on any platform. Remember, React Native  isn't some product - it's a community of thousands of developers. So if you're interested in React Native, here's some related stuff you might want to check out.

- [Fetching Data with React Native](https://facebook.github.io/react-native/docs/network.html)
- [Navigation](https://facebook.github.io/react-native/docs/using-navigators.html)
- [React Native Express](http://www.reactnativeexpress.com/)
- [The Future](https://getexponent.com/)

## Resources

- [React Native Docs](https://facebook.github.io/react-native/docs/tutorial.html)
- [How to Build a React Native App](https://www.raywenderlich.com/126063/react-native-tutorial)
- [React Native Debugger](https://github.com/jhen0409/react-native-debugger)
- [Debugging a React Native App](http://facebook.github.io/react-native/docs/debugging.html#content)
- [Awesome React Native](https://github.com/jondot/awesome-react-native)
- [How to Use the ListView Component](https://medium.com/differential/react-native-basics-how-to-use-the-listview-component-a0ec44cf1fe8#.ck2vfr4ue)
- [React Native Libraries and Plugins](https://js.coach/react-native)
- [React Native Development Tools](https://facebook.github.io/react-native/docs/more-resources.html#development-tools)

## Screencasts 

- [WDI 12](https://www.youtube.com/watch?v=1vi2o8xOTKI)
