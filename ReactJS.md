React is a free and open-source front-end JavaScript library which is used to develop various interactive user-interfaces. It is a simple, feature rich and component based UI library. When we say component based, we mean that React develops applications by creating various reusable and independent codes. This UI library is, thus, widely used in web development.

React can be used to develop small applications as well as big, complex applications. React provides minimal and solid feature set to kick-start a web application. React community compliments React library by providing large set of ready-made components to develop web application in a record time. React community also provides advanced concept like state management, routing, etc., on top of the React library.

## React versions

React library was founded by Jordan Walke, a software engineer at Facebook in 2011. Then the initial version, 0.3.0 of React is released on May, 2013 and the latest version, _17.0.1_ is released on October, 2020. The major version introduces breaking changes and the minor version introduces new feature without breaking the existing functionality. Bug fixes are released as and when necessary. React follows the _Semantic Versioning (semver)_ principle.

## What is the need for React?

Even though there are various libraries that provide a medium to develop user-interfaces, React still stands tall in terms of popularity. Here's why −

- **Component Based** − React makes use of multiple components to build an application. These components are independent and have their own logic which makes them reusable throughout the development process. This will drastically reduce the application's development time.
- **Better and Faster Performance** − React uses Virtual DOM. Virtual DOM compares the previous states of components of an application with the current states and only updates the changes in Real DOM. Whereas, conventional web applications update all components again. This helps React in creating web applications faster.
- **Extremely Flexible** − React allows developers and teams to set their own conventions that they deem are best suited and implement it however they see fit, as there are no strict rules for code conventions in React.
- **Creates dynamic applications easily** − Dynamic web applications require less coding while offering more functionality. Thus, React can create them easily.
- **Develops Mobile Applications as well** − Not only web applications, React can also develop mobile applications using React Native. React Native is an open-source UI software framework that is derived from React itself. It uses React Framework to develop applications for Android, macOS, Web, Windows etc.
- **Debugging is Easy** − The data flow in React is unidirectional, i.e., while designing an app using React, child components are nested within parent components. As the data flows is in a single direction, it gets easier to debug errors and spot the bugs.

## Applications

Few popular websites powered by _React library_ are listed below −

- _Facebook_, popular social media application − React was originally developed at Facebook (or Meta), so its only natural that they use it to run their application. As for their mobile application, it uses React Native to display Android and iOS components, instead of DOM. Facebook's codebase now includes over 20,000 components and uses the React version that is public.
- _Instagram_, popular photo sharing application − Instagram is also completely based on React, as it is powered by Meta as well. The main features to show its usage include Geo-locations, Hashtags, Google Maps APIs etc.
- _Netflix_, popular media streaming application − Netflix switched to React in 2015. The factors that mainly influenced this decision are the 1) startup speed to reduce the processing time to render the homepage and enabling dynamic elements in the UI, 2) modularity to allow various features that must coexist with the control experience and 3) runtime performance for efficient UI rendering.
- _Code Academy_, popular online training application − Code Academy uses React as the "script is battle-tested, easy to think about, makes SEO easy and is compatible with legacy code and flexible enough for the future".
- _Reddit_, popular content sharing application − Reddit is also developed using React from the scratch.

# React - Installation

React provides CLI tools for the developer to fast forward the creation, development and deployment of the React based web application. React CLI tools depends on the Node.js and must be installed in your system. Hopefully, you have installed Node.js on your machine. We can check it using the below command −

```
node --version
```

You could see the version of Nodejs you might have installed. It is shown as below for me,

```
v24.11.0
```

## Toolchain

To develop lightweight features such as form validation, model dialog, etc., React library can be directly included into the web application through content delivery network (CDN). It is similar to using jQuery library in a web application. For moderate to big application, it is advised to write the application as multiple files and then use bundler such as webpack, parcel, rollup, etc., to compile and bundle the application before deploying the code.

React toolchain helps to create, build, run and deploy the React application. React toolchain basically provides a starter project template with all necessary code to bootstrap the application.

Some of the popular toolchain to develop React applications are −

- **Create React App** − SPA oriented toolchain
- **Next.js** − server-side rendering oriented toolchain
- **Gatsby** − Static content oriented toolchain

Tools required to develop a React application are −

- The _serve_, a static server to serve our application during development
- Babel compiler
- Create React App CLI

Let us learn the basics of the above mentioned tools and how to install those in this chapter.

## The _serve_ static server

The _serve_ is a lightweight web server. It serves static site and single page application. It loads fast and consume minimum memory. It can be used to serve a React application. Let us install the tool using _npm_ package manager in our system.

```
npm install serve -g
added 86 packages in 6s
```

Let us create a simple static site and serve the application using _serve_ app.

Open a command prompt and go to your workspace.

```
cd /go/to/your/workspace
```

Create a new folder, _static_site_ and change directory to newly created folder.

```
mkdir static_site 
cd static_site
```

Next, create a simple webpage inside the folder using your favorite html editor.

### index.html

```
<!DOCTYPE html> 
<html> 
   <head> 
      <meta charset="UTF-8" /> 
      <title>Static website</title> 
   </head> 
   <body> 
      <div><h1>Hello!</h1></div> 
   </body> 
</html>
```

Next, run the _serve_ command.

```
serve .
```

We can also serve single file, _index.html_ instead of the whole folder.

```
serve ./index.html
```

Next, open the browser and enter _http://localhost:3000_ in the address bar and press enter. serve application will serve our webpage as shown below.

![Hello](https://www.tutorialspoint.com/reactjs/images/hello.jpg)

The serve will _serve_ the application using default port, 3000. If it is not available, it will pick up a random port and specify it.

   ```
   ââââââââââââââââââââââââââââââââââââââââââââââ
   â                                            â
   â   Serving!                                 â
   â                                            â
   â   - Local:    http://localhost:3000        â
   â   - Network:  http://192.168.28.173:3000   â
   â                                            â
   â   Copied local address to clipboard!       â
   â                                            â
   ââââââââââââââââââââââââââââââââââââââââââââââ
   ```

## Babel compiler

Babel is a JavaScript compiler which compiles many variant (es2015, es6, etc.,) of JavaScript into standard JavaScript code supported by all browsers. React uses JSX, an extension of JavaScript to design the user interface code. Babel is used to compile the JSX code into JavaScript code.

To install Babel and it's React companion, run the below command −

```
npm install babel-cli babel-preset-react-app -g
... 
... 
added 397 packages in 16s
```

Babel helps us to write our application in next generation of advanced JavaScript syntax.

## Create React App toolchain

_Create React App_ is a modern CLI tool to create single page React application. It is the standard tool supported by React community. It handles babel compiler as well. Let us install _Create React App_ in our local system.

```
> npm install -g create-react-app
added 64 packages in 3s
```

### Updating the toolchain

_React Create App_ toolchain uses the react-scripts package to build and run the application. Once we started working on the application, we can update the react-script to the latest version at any time using _npm_ package manager.

```
npm install react-scripts@latest
added 1330 packages in 42s
```

### Advantages of using React toolchain

React toolchain provides lot of features out of the box. Some of the advantages of using React toolchain are −

- Predefined and standard structure of the application.
- Ready-made project template for different type of application.
- Development web server is included.
- Easy way to include third party React components.
- Default setup to test the application.

# React - Features

React slowly becoming one of the best JavaScript framework among web developers. It is playing an essential role in the front-end ecosystem. Following are the important features of React

- Virtual DOM
- Components
- JSX
- One way data binding
- Scalable
- Flexible
- Modular

### Virtual DOM

Virtual DOM is a special DOM created by React. Virtual DOM represents the real DOM of the current HTML document. Whenever there is a change in the HTML document, React checks the updated virtual DOM with the previous state of the Virtual DOM and update only the different in th actual / real DOM. This improves the performance of the rendering of the HTML document.

For example, if we create a React component to show the current time by periodically updating the time through **setInterval()** method, then React will update only the current time instead of updating the whole content of the component.

### Components

React is build upon the concept of components. All modern front end framework relies on the component architecture. Component architecture enables the developer to break down the large application into smaller components, which can be further break down into even smaller component. Breaking down the application into smaller component simplifies the application and make it more understandable and manageable.

### JSX

JSX is a JavaScript extension to create arbitrary HTML element using syntax similar to HTML. This will simplify the HTML document creation as well as easy to understand the document. React will convert the JSX into JavaScript object consisting of React's createElement() function call before executing it. It improves the performance of the application. Also, React allows the HTML document creation using pure **createElement()** function without JSX as well. This enables the developer to directly create HTML document in a situation where JSX does not fit well.

### One way data binding

One way data binding prevents the data in a component to flow backward. A component can pass the data to its child component only. The data cannot be passed by a component to its parent component in any situation. This will simplify the data handling and reduce the complexity. Two way data binding may seems mandatory at first but a closer look suggests that the application can be done with one way data binding only and this simplifies the application concept as well.

### Scalable

React can be used to create application of any size. React component architecture, Virtual DOM and one way data binding properly handle large application in a reasonable time frame required for a front end application. These features make React a scalable solution.

### Flexible

React only provides only few basic concept to create truly scalable application. React does not restrict the developer in any way to follow a rigid process. This enables the developer to apply their own architecture on top the basic concept and makes it flexible.

### Modular

React component can be created in a separate JavaScript file and can be made exportable. This enables the developer to categories and group certain components into a module so that it can be imported and used wherever needed.

# React - Architecture

React library is built on a solid foundation. It is simple, flexible and extensible. As we learned earlier, React is a library to create user interface in a web application. React's primary purpose is to enable the developer to create user interface using pure JavaScript. Normally, every user interface library introduces a new template language (which we need to learn) to design the user interface and provides an option to write logic, either inside the template or separately.

Instead of introducing new template language, React introduces three simple concepts as given below −

### React elements

JavaScript representation of HTML DOM. React provides an API, _**React.createElement**_ to _create React Element_.

### JSX

A JavaScript extension to design user interface. JSX is an XML based, extensible language supporting HTML syntax with little modification. JSX can be compiled to React Elements and used to create user interface.

### React component

React component is the primary building block of the React application. It uses React elements and JSX to design its user interface. React component is basically a JavaScript class (extends the **_React.component_** class) or pure JavaScript function. React component has properties, state management, life cycle and event handler. React component can be able to do simple as well as advanced logic.

## Architecture of the React Application

React library is just UI library and it does not enforce any particular pattern to write a complex application. Developers are free to choose the design pattern of their choice. React community advocates certain design pattern. One of the patterns is Flux pattern. React library also provides lot of concepts like Higher Order component, Context, Render props, Refs etc., to write better code. React Hooks is evolving concept to do state management in big projects. Let us try to understand the high level architecture of a React application.

![React App](https://www.tutorialspoint.com/reactjs/images/react_app.jpg)

- React app starts with a single root component.
- Root component is build using one or more component.
- Each component can be nested with other component to any level.
- Composition is one of the core concepts of React library. So, each component is build by composing smaller components instead of inheriting one component from another component.
- Most of the components are user interface components.
- React app can include third party component for specific purpose such as routing, animation, state management, etc.

## Workflow of a React application

Open a command prompt and go to your workspace.

```
cd /go/to/your/workspace
```

Next, create a folder, _static_site_ and change directory to newly created folder.

```
mkdir static_site 
cd static_site
```

### Example

Next, create a file, _hello.html_ and write a simple React application.

```
<!DOCTYPE html> 
<html> 
   <head> 
      <meta charset="UTF-8" /> 
      <title>React Application</title> 
   </head> 
   <body> 
  <div id="react-app"></div>  
  <script type="module">
     import React from 'https://esm.sh/react';
     import ReactDOM from 'https://esm.sh/react-dom/client';

     function App() {
        return React.createElement('h1', null, 'Hello React!');
     }

     const root = ReactDOM.createRoot(document.getElementById('react-app'));
     root.render(React.createElement(App));
   </script> 
</body> 
</html>
```

Next, serve the application using serve web server.

```
	serve ./hello.html
```

### Output

Next, open your favorite browser. Enter **http://localhost:3000** in the address bar and then press enter.

![React Hello](https://www.tutorialspoint.com/reactjs/images/react_hello.jpg)

Let us analyse the code and do little modification to better understand the React application.

Here, we are using three API provided by the React library.

## The Important Part – The Root Div

```
<div id="react-app"></div>
```
This is VERY important.
Think of it like:  “Hey React, put your application inside this box.”
React will control everything inside this div.

## Script Type Module

```
<script type="module">
```

This means:

- You can use `import`
- It works like modern JavaScript

Without `type="module"`, `import` would not work.
## Importing React

```
import React from 'https://esm.sh/react';  
import ReactDOM from 'https://esm.sh/react-dom/client';
```

Normally, React is installed using npm.

But here you are loading it directly from the internet using a CDN (`esm.sh`).

### What are these?

- **React** → Library to build UI
- **ReactDOM** → Used to show React inside the browser DOM

## Creating a Component

```
function App() {  
   return React.createElement('h1', null, 'Hello React!');  
}
```
This is called a **Component**.

Think of it like: A function that returns UI.
### What does this line mean?

```
React.createElement('h1', null, 'Hello React!')
```

It means:
- Create an `<h1>` tag
- No attributes (`null`)
- Put text inside: `"Hello React!"`

So basically:  `<h1>Hello React!</h1>`

But written in React style.
## Connecting React to HTML

```
const root = ReactDOM.createRoot(document.getElementById('react-app'));
```

This means:

1. Find the div: `<div id="react-app"></div>`
2. Tell React:  “You control this div now.”
## Rendering the App

```
root.render(React.createElement(App));
```

This means:  Render the `App` component inside the root div.
So the browser will show: `Hello React!`

# How Everything Connects

```
Browser loads HTML  
⬇  
React gets imported  
⬇  
React creates `<h1>`  
⬇  
React puts it inside `<div id="react-app">`  
⬇  
You see: **Hello React!**
```
# Why No JSX?

Usually people write React like this:

```
function App() {  
  return <h1>Hello React!</h1>;  
}
```
But browsers don’t understand JSX directly.
So you're using: `React.createElement()`
Which is the raw version of JSX.
JSX is just a shortcut for `createElement`.

### React.createElement

Used to create React elements. It expects three parameters −

- Element tag
- Element attributes as object
- Element content - It can contain nested React element as well

### ReactDOM.createRoot

Used to create main container element.

### root.render

Used to render the element into the container. It expects a single parameter −

- React Element OR JSX

### Nested React element

As _**React.createElement**_ allows nested React element, let us add nested element as shown below −

**Example**

```
<script type="module">
     const root = ReactDOM.createRoot(document.getElementById('react-app'));
     root.render(React.createElement(App)); 
</script>
```

**Output**

It will generate the below content −

```
<div><h1> Hello React!</h1></div> 
```

### Use JSX

Next, let us remove the React element entirely and introduce JSX syntax as shown below −

```
<!DOCTYPE html> 
<html> 
   <head> 
      <meta charset="UTF-8" /> 
      <title>React Application</title> 
   </head> 
   <body> 
      <div id="react-app"></div> 
      <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script> 
      <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script> 
      <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script> 
      <script type="text/babel"> 
         ReactDOM.render(
            <div><h1>Hello JSX!</h1></div>, 
            document.getElementById('react-app') 
         ); 
     </script> 
   </body> 
</html>
```

Here, we have included babel to convert JSX into JavaScript and added _type="text/babel"_ in the script tag.

```
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script> 
<script type="text/babel"> 
   ... 
   ... 
</script>
```

Next, run the application and open the browser. The output of the application is as follows −

![Hello Jsx](https://www.tutorialspoint.com/reactjs/images/hello_jsx.jpg)

Next, let us create a new React component, Greeting and then try to use it in the webpage.

```
<script type="text/babel"> 
   function Greeting() {
      return <div><h1>Hello JSX!</h1></div> 
   } 
   ReactDOM.render(<Greeting />, document.getElementById('react-app') ); 
</script>
```

The result is same and as shown below −

![Hello Jsx](https://www.tutorialspoint.com/reactjs/images/hello_jsx.jpg)

By analyzing the application, we can visualize the workflow of the React application as shown in the below diagram.

![Workflow Jsx](https://www.tutorialspoint.com/reactjs/images/workflow_jsx.jpg)

React app calls **_ReactDOM.render_** method by passing the user interface created using React component (coded in either JSX or React element format) and the container to render the user interface.

**_ReactDOM.render_** processes the JSX or React element and emits Virtual DOM.

Virtual DOM will be merged and rendered into the container.

# React - Using JSX

consider the following code:

```
const element = <h1>Hello React!</h1>
```

The tag provided in the code above is known as JSX. JSX is mainly used to provide information about the appearance of an interface. However, it is not completely a template language but a syntax extension to JavaScript. JSX produces elements that are rendered into a DOM, in order to specify what the output must look like.

## Using JSX in ReactJS

JSX enables the developer to create a virtual DOM using XML syntax. It compiles down to pure JavaScript (_React.createElement function calls_), therefore, it can be used inside any valid JavaScript code.

- Assign to a variable.

```
var greeting = <h1>Hello React!</h1>
```

- Assign to a variable based on a condition.

```
var canGreet = true; 
if(canGreet) { 
   greeting = <h1>Hello React!</h1> 
}
```

- Can be used as return value of a function.

```
function Greeting() { 
   return <h1>Hello React!</h1> 
   
} 
greeting = Greeting()
```

- Can be used as argument of a function.

```
function Greet(message) { 
   const root = ReactDOM.createRoot(document.getElementById('react-app'));
   root.render(message);
} 
Greet(<h1>Hello React!</h1>)
```

## Why JSX?

Using JSX with React is not mandatory, as there are various options to achieve the same thing as JSX; but it is helpful as a visual aid while working with UI inside a JavaScript code.

- JSX performs optimization while translating the code to JavaScript, making it faster than regular JavaScript.
- React uses components that contain both markup and logic in a single file, instead of separate files.
- Most of the errors can be found at compilation time, as the data flow is unidirectional.
- Creating templates becomes easier with JSX.
- We can use JSX inside of conditional statements (if−else) and loop statements (for loops), can assign it to variables, accept it as arguments, or return it from functions.
- Using JSX can prevent Cross Site Scripting attacks, or injection attacks.

## Expressions in JSX

JSX supports expression in pure JavaScript syntax. Expression has to be enclosed inside the curly braces, **_{ }_**. Expression can contain all variables available in the context, where the JSX is defined. Let us create simple JSX with expression.

### Example

```
<script type="text/babel">
   var cTime = new Date().toTimeString();
   ReactDOM.render(
      <div><p>The current time is {cTime}</p></div>, 
      document.getElementById('react-app') );
</script>
```

### Output

Here, _cTime_ used in the JSX using expression. The output of the above code is as follows,

```
The Current time is 21:19:56 GMT+0530(India Standard Time)
```

One of the positive side effects of using expression in JSX is that it prevents _Injection attacks_ as it converts any string into html safe string.

## Functions in JSX

JSX supports user defined JavaScript function. Function usage is similar to expression. Let us create a simple function and use it inside JSX.

### Example

```
<script type="text/babel">
   var cTime = new Date().toTimeString();
   ReactDOM.render(
      <div><p>The current time is {cTime}</p></div>, 
      document.getElementById('react-app') 
   );
</script>
```

### Output

Here, _getCurrentTime()_ is used get the current time and the output is similar as specified below −

```
The Current time is 21:19:56 GMT+0530(India Standard Time)
```

## Attributes in JSX

JSX supports HTML like attributes. All HTML tags and its attributes are supported. Attributes has to be specified using camelCase convention (and it follows JavaScript DOM API) instead of normal HTML attribute name. For example, class attribute in HTML has to be defined as _className_. The following are few other examples −

- _htmlFor_ instead of _for_
- _tabIndex_ instead of _tabindex_
- _onClick_ instead of _onclick_

### Example

```
<style>
   .red { color: red }
</style>
<script type="text/babel">
   function getCurrentTime() {
      return new Date().toTimeString();
   }
   ReactDOM.render(
      <div>
         <p>The current time is <span className="red">{getCurrentTime()}</span></p>
      </div>,
      document.getElementById('react-app') 
   );
</script>
```

### Output

The output is as follows −

```
The Current time is 22:36:55 GMT+0530(India Standard Time)
```

## Using Expression within Attributes

JSX supports expression to be specified inside the attributes. In attributes, double quote should not be used along with expression. Either expression or string using double quote has to be used. The above example can be changed to use expression in attributes.

```
<style>
   .red { color: red }
</style>

<script type="text/babel">
   function getCurrentTime() {
      return new Date().toTimeString();
   }
   var class_name = "red";
   ReactDOM.render(
      <div>
         <p>The current time is <span className={class_name}>{getCurrentTime()}</span></p>
      </div>, 
      document.getElementById('react-app') 
   );
</script>
```

## Nested Elements in JSX

Nested elements in JSX can be used as JSX Children. They are very useful while displaying the nested components. You can use any type of elements together including tags, literals, functions, expressions etc. But false, null, undefined, and true are all valid elements of JSX; they just don't render as these JSX expressions will all render to the same thing. In this case, JSX is similar to HTML.

Following is a simple code to show the usage of nested elements in JSX −

```
<div>
   This is a list:
   <ul>
      <li>Element 1</li>
      <li>Element 2</li>
   </ul>
</div>
```

# React - Components

React component is the building block of a React application. 

A React component represents a small chunk of user interface in a webpage. The primary job of a React component is to render its user interface and update it whenever its internal state is changed. In addition to rendering the UI, it manages the events belongs to its user interface. To summarize, React component provides below functionalities.

- Initial rendering of the user interface.
- Management and handling of events.
- Updating the user interface whenever the internal state is changed.

React component accomplish these feature using three concepts −

- **Properties** − Enables the component to receive input.
- **Events** − Enable the component to manage DOM events and end-user interaction.
- **State** − Enable the component to stay stateful. Stateful component updates its UI with respect to its state.

There are two types of components in React. They are −

- **Function Components**
- **Class Components**

## Function Components

A function component is literally defined as JavaScript functions. This React component accepts a single object argument and returns a React element. Note that an element in React is not a component, but a component is comprised of multiple elements. Following is the syntax for the function component in React:

```
function function_name(argument_name) {
   function_body;
}
```

## Class Components

Similarly, class components are basic classes that are made of multiple functions. All class components of React are subclasses of the **React.Component** class, hence, a class component must always extend it. Following is the basic syntax −

```
class class_name extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## Creating a React component

React library has two component types. The types are categorized based on the way it is being created.

- Function component − Uses plain JavaScript function.
- ES6 class component − Uses ES6 class.

The core difference between function and class component are −

- Function components are very minimal in nature. Its only requirement is to return a _React element_.

```
function Hello() { 
   return '<div>Hello</div>' 
}
```

The same functionality can be done using ES6 class component with little extra coding.

```
class ExpenseEntryItem extends React.Component {         
   render() { 
      return ( 
         <div>Hello</div> 
      ); 
   }
}
```

- Class components supports state management out of the box whereas function components does not support state management. But, React provides a hook, _useState()_ for the function components to maintain its state.
- Class component have a life cycle and access to each life cycle events through dedicated callback apis. Function component does not have life cycle. Again, React provides a hook, _useEffect()_ for the function component to access different stages of the component.

### Create a skeleton application

Open a terminal and go to your workspace.

```
> cd /go/to/your/workspace
```

Next, create a new React application using _Create React App_ tool.

```
> create-react-app react-expense-app
```

It will a create new folder **react-expense-app** with startup template code.

Next, go to **expense-manager** folder and install the necessary library.

```
cd react-expense-app 
npm install
```

The _npm install_ will install the necessary library under _node_modules_ folder.
## Creating a class component

Let us create a new React component (in our expense-manager app), ExpenseEntryItem to showcase an expense entry item. Expense entry item consists of name, amount, date and category. The object representation of the expense entry item is −

```
{ 
   'name': 'Mango juice', 
   'amount': 30.00, 
   'spend_date': '2020-10-10' 
   'category': 'Food', 
}
```

Open _expense-manager_ application in your favorite editor.

Next, create a file, _ExpenseEntryItem.css_ under _src/components_ folder to style our component.

Next, create a file, _ExpenseEntryItem.js_ under _src/components_ folder by extending _React.Component_.

```
import React from 'react'; 
import './ExpenseEntryItem.css'; 
class ExpenseEntryItem extends React.Component { 
}
```

Next, create a method _render_ inside the _ExpenseEntryItem_ class.

```
class ExpenseEntryItem extends React.Component { 
   render() { 
   } 
}
```

Next, create the user interface using JSX and return it from _render_ method.

```
class ExpenseEntryItem extends React.Component {
   render() {
      return (
         <div>
            <div><b>Item:</b> <em>Mango Juice</em></div>
            <div><b>Amount:</b> <em>30.00</em></div>
            <div><b>Spend Date:</b> <em>2020-10-10</em></div>
            <div><b>Category:</b> <em>Food</em></div>
         </div>
      );
   }
}
```

Next, specify the component as default export class.

```
import React from 'react';
import './ExpenseEntryItem.css';

class ExpenseEntryItem extends React.Component {
   render() {
      return (
         <div>
            <div><b>Item:</b> <em>Mango Juice</em></div>
            <div><b>Amount:</b> <em>30.00</em></div>
            <div><b>Spend Date:</b> <em>2020-10-10</em></div>
            <div><b>Category:</b> <em>Food</em></div>
         </div>
      );
   }
}
export default ExpenseEntryItem;
```

Now, we successfully created our first React component. Let us use our newly created component in _index.js_.

```
import React from 'react';
import { createRoot } from 'react-dom/client';
import ExpenseEntryItem from './components/ExpenseEntryItem'

const container = document.getElementById('root');
const root = createRoot(container);

root.render(<ExpenseEntryItem />);
```

### Example

The same functionality can be done in a webpage using CDN as shown below −

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8" />
      <title>React application :: ExpenseEntryItem component</title>
   </head>
   <body>
      <div id="react-app"></div>
       
      <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
      <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
      <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
      <script type="text/babel">
         class ExpenseEntryItem extends React.Component {
            render() {
               return (
                  <div>
                     <div><b>Item:</b> <em>Mango Juice</em></div>
                     <div><b>Amount:</b> <em>30.00</em></div>
                     <div><b>Spend Date:</b> <em>2020-10-10</em></div>
                     <div><b>Category:</b> <em>Food</em></div>
                  </div>
               );
            }
         }
         ReactDOM.render(
            <ExpenseEntryItem />,
            document.getElementById('react-app') );
      </script>
   </body>
</html>
```

Next, serve the application using npm command.

```
npm start
```

### Output

Next, open the browser and enter _http://localhost:3000_ in the address bar and press enter.

```
Item: Mango Juice
Amount: 30.00
Spend Date: 2020-10-10
Category: Food
```

## Creating a function component

React component can also be created using plain JavaScript function but with limited features. Function based React component does not support state management and other advanced features. It can be used to quickly create a simple component.

The above _ExpenseEntryItem_ can be rewritten in function as specified below −

```
function ExpenseEntryItem() {
   return (
      <div>
         <div><b>Item:</b> <em>Mango Juice</em></div>
         <div><b>Amount:</b> <em>30.00</em></div>
         <div><b>Spend Date:</b> <em>2020-10-10</em></div>
         <div><b>Category:</b> <em>Food</em></div>
      </div>
   );
}
```

Here, we just included the render functionality and it is enough to create a simple React component.

## Splitting Components

Even if JavaScript is said to be simpler to execute, there are many times where the code gets complex due to large number of classes or dependencies for a relatively simple project. And with larger codes, the loading time in a browser gets longer. As a result, reducing the efficiency of its performance. This is where code-splitting can be used. Code splitting is used to divide components or bundles into smaller chunks to improve the performance.

Code splitting will only load the components that are currently needed by the browser. This process is known as lazy load. This will drastically improve the performance of your application. One must observe that we are not trying to reduce the amount of code with this, but just trying to reduce the burden of browser by loading components that the user might never need. Let us look at an example code.

### Example

Let us first see the bundled code of a sample application to perform any operation.

```
	// file name = app.js
	import { sub } from './math.js';
	
	console.log(sub(23, 14));
	// file name = math.js
	export function sub(a, b) {
	  return a - b;
	}
```

The Bundle for the application above will look like this −

```
function sub(a, b) {
  return a - b;
}
console.log(sub(23, 14));
```

Now, the best way to introduce code splitting in your application is by using dynamic import().

```
// Before code-splitting
import { sub } from './math';

console.log(add(23, 14));

// After code-splitting
import("./math").then(math => {
  console.log(math.sub(23, 14));
});
```

When this syntax is used (in bundles like Webpack), code-splitting will automatically begin. But if you are Create React App, the code-splitting is already configured for you and you can start using it immediately.

# React - Using Newly Created Components

A React component represents a small chunk of user interface in a webpage. The primary job of a React component is to render its user interface and update it whenever its internal state is changed. In addition to rendering the UI, it manages the events belongs to its user interface. To summarize, React component provides below functionalities.

## Using Components in ReactJS

**Step 1** − Open our _expense-manager_ application in your favorite editor and open the _ExpenseEntryItem.js_ file.

Then, import _FormattedMoney_ and _FormattedDate_ using the following statements.

```
import FormattedMoney from './FormattedMoney' 
import FormattedDate from './FormattedDate'
```

**Step 2** − Next, update the _render_ method by including _FormattedMoney_ and _FormattedDater_ component.

```
render() {
   return (
      <div>
         <div><b>Item:</b> <em>{this.props.item.name}</em></div>
         <div><b>Amount:</b> 
            <em>
               <FormattedMoney value={this.props.item.amount} />
            </em>
         </div>
         <div><b>Spend Date:</b> 
            <em>
               <FormattedDate value={this.props.item.spendDate} />
            </em>
         </div>
         <div><b>Category:</b> 
            <em>{this.props.item.category}</em></div>
      </div>
   );
}
```

Here, we have passed the amount and spendDate through value attribute of the components.

The final updated source code of the ExprenseEntryItem component is given below −

```
import React from 'react'
import FormattedMoney from './FormattedMoney'
import FormattedDate from './FormattedDate'

class ExpenseEntryItem extends React.Component {
   constructor(props) {
      super(props);
   }
   render() {
      return (
         <div>
            <div><b>Item:</b> <em>{this.props.item.name}</em></div>
            <div><b>Amount:</b> 
               <em>
                  <FormattedMoney value={this.props.item.amount} />
               </em>
            </div>
            <div><b>Spend Date:</b> 
               <em>
                  <FormattedDate value={this.props.item.spendDate} />
               </em>
            </div>
            <div><b>Category:</b> 
               <em>{this.props.item.category}</em></div>
         </div>
      );
   }
}
export default ExpenseEntryItem;
```

**index.js**

Open _index.js_ and call the _ExpenseEntryItem_ component by passing the item object.

```
import React from 'react';
import { createRoot } from 'react-dom/client';
import ExpenseEntryItem from './components/ExpenseEntryItem'

const container = document.getElementById('root');
const root = createRoot(container);

const item = {
   id: 1, 
   name : "Grape Juice", 
   amount : 30.5, 
   spendDate: new Date("2025-11-08"), 
   category: "Food" 
}

root.render(<ExpenseEntryItem item={item}/>);
```

Next, serve the application using npm command.

```
npm start
```

Open the browser and enter _http://localhost:3000_ in the address bar and press enter.

![Grape Modules](https://www.tutorialspoint.com/reactjs/images/grapes_modules.jpg)

# What is `map()` in React?

`map()` is actually a **JavaScript array function**, not a React function.

React uses it to:

> 🔁 Loop through an array  
> 📦 Convert each item into JSX  
> 🎨 Render lists dynamically
# Normal JavaScript Example

```
const numbers = [1, 2, 3];  
  
const doubled = numbers.map((num) => {  
  return num * 2;  
});  
  
console.log(doubled);   
// [2, 4, 6]
```

👉 `map()` takes each element  
👉 Runs a function  
👉 Returns a new array
# Why We Use `map()` in React

In React, we often have data like this:

```
const expenses = [  
  { id: 1, name: "Grape Juice", amount: 30 },  
  { id: 2, name: "Milk", amount: 20 },  
  { id: 3, name: "Bread", amount: 15 }  
];
```

We want to display all of them.
Instead of writing components manually, we use `map()`.
# Example in React

```
function App() {  
  const expenses = [  
    { id: 1, name: "Grape Juice", amount: 30 },  
    { id: 2, name: "Milk", amount: 20 },  
    { id: 3, name: "Bread", amount: 15 }  
  ];  
  
  return (  
    <div>  
      {expenses.map((expense) => (  
        <div key={expense.id}>  
          <h3>{expense.name}</h3>  
          <p>{expense.amount}</p>  
        </div>  
      ))}  
    </div>  
  );  
}
```

# What’s Happening Here?

```
expenses.map((expense) => (
```

For each expense:

1. Take one object
2. Return JSX
3. React renders all returned JSX

So it becomes like:

```
<div>  
  <div>Grape Juice</div>  
  <div>Milk</div>  
  <div>Bread</div>  
</div>
```

# VERY IMPORTANT: `key` Prop

Whenever you use `map()` in React, you **must** add a `key`.

```
<div key={expense.id}>
```

Why?

React uses `key` to:
- Identify elements
- Update efficiently
- Prevent bugs

❌ Don’t use index as key (unless necessary)

# Real World Pattern (Component Version)

Since you're building components like `ExpenseEntryItem`, you’ll do this:

```
{expenses.map((expense) => (  
  <ExpenseEntryItem   
    key={expense.id}  
    item={expense}  
  />  
))}
```
# What is `() => {}` ?

It’s called an **Arrow Function**.
It’s just a **shorter way to write a function** in modern JavaScript.
# Normal Function (What You Know)

```
function sayHello() {  
  console.log("Hello");  
}
```
# Same Thing Using Arrow Function

```
const sayHello = () => {  
  console.log("Hello");  
};
```

# Now Let’s Compare Clearly

### Normal function:

```
function add(a, b) {  
  return a + b;  
}
```

### Arrow function:

```
const add = (a, b) => {  
  return a + b;  
};
```

# Why React Uses Arrow Functions So Much?

Because React code uses a lot of:

- Callbacks
- Inline functions
- `map()`
- Event handlers

Example:

```
expenses.map(function(expense) {  
  return expense.name;  
});
```

Modern way:

```
expenses.map((expense) => {  
  return expense.name;  
});
```
# Even Shorter Version

If there is only **one line return**, you can remove `{}` and `return`.

```
expenses.map((expense) => expense.name);
```
# Let’s Connect to Your React Example

You saw this:

```
expenses.map((expense) => (  
  <ExpenseEntryItem key={expense.id} item={expense} />  
))
```

This is same as writing:

```
expenses.map(function(expense) {  
  return (  
    <ExpenseEntryItem key={expense.id} item={expense} />  
  );  
});
```

# React - Component Collection

In modern application, developer encounters a lot of situations, where list of item (e.g. todos, orders, invoices, etc.,) has to be rendered in tabular format or gallery format. React provides clear, intuitive and easy technique to create list based user interface. React uses two existing features to accomplish this feature.

- JavaScript's built-in _map_ method.
- React expression in jsx.

## Map Method

The _map_ function accepts a collection and a mapping function. The map function will be applied to each and every item in the collection and the results are used to generate a new list.

For instance, declare a JavaScript array with 5 random numbers as shown below −

```
let list = [10, 30, 45, 12, 24]
```

Now, apply an anonymous function, which double its input as shown below −

```
result = list.map((input) => input * 2);
```

Then, the resulting list is −

```
[20, 60, 90, 24, 48]
```

To refresh the React expression, let us create a new variable and assign a React element.

```
var hello = <h1>Hello!</h1> 
var final = <div>{helloElement}</div>
```

Now, the React expression, _hello_ will get merged with _final_ and generate,

```
<div><h1>Hello!</h1></div>
```

### Example

Let us apply this concept to create a component to show a collection of expense entry items in a tabular format.

**Step 1** − Open our _expense-manager_ application in your favorite editor.

Create a file _ExpenseEntryItemList.css_ in _src/components_ folder to include styles for the component.

Create another file, _ExpenseEntryItemList.js_ in _src/components_ folder to create _ExpenseEntryItemList_ component

**Step 2** − Import React library and the stylesheet.

```
import React from 'react'; 
import './ExpenseEntryItemList.css';
```

**Step 3** − Create _ExpenseEntryItemList_ class and call constructor function.

```
class ExpenseEntryItemList extends React.Component {  
   constructor(props) { 
      super(props); 
   } 
}
```

Create a _render()_ function.

```
render() { 
}
```

**Step 4** − Use the _map_ method to generate a collection of HTML table rows each representing a single expense entry item in the list.

```
render() {
   const lists = this.props.items.map( (item) => 
      <tr key={item.id}>
         <td>{item.name}</td>
         <td>{item.amount}</td>
         <td>{new Date(item.spendDate).toDateString()}</td>
         <td>{item.category}</td>
      </tr>
   );
}
```

Here, _key_ identifies each row and it has to be unique among the list.

**Step 5** − Next, in the _render()_ method, create a HTML table and include the _lists_ expression in the rows section.

```
return (
   <table>
      <thead>
         <tr>
            <th>Item</th>
            <th>Amount</th>
            <th>Date</th>
            <th>Category</th>
         </tr>
      </thead>
      <tbody>
         {lists}
      </tbody>
   </table>
);
```

Finally, export the component.

```
export default ExpenseEntryItemList;
```

Now, we have successfully created the component to render the expense items into HTML table. The complete code is as follows −

```
import React from 'react';
import './ExpenseEntryItemList.css'

class ExpenseEntryItemList extends React.Component {
   constructor(props) {
      super(props);
   }
   render() {
      const lists = this.props.items.map( (item) => 
         <tr key={item.id}>
            <td>{item.name}</td>
            <td>{item.amount}</td>
            <td>{new Date(item.spendDate).toDateString()}</td>
            <td>{item.category}</td>
         </tr>
      );
      return (
         <table>
            <thead>
               <tr>
                  <th>Item</th>
                  <th>Amount</th>
                  <th>Date</th>
                  <th>Category</th>
               </tr>
            </thead>
            <tbody>
               {lists}
            </tbody>
         </table>
      );
   }
}
export default ExpenseEntryItemList;
```

**index.js:**

Open _index.js_ and import our newly created _ExpenseEntryItemList_ component.

```
import ExpenseEntryItemList from './components/ExpenseEntryItemList'
```

Next, declare a list (of expense entry item) and populate it with some random values in _index.js_ file.

```
const items = [
   { id: 1, name: "Pizza", amount: 80, spendDate: "2025-10-10", category: "Food" },
   { id: 1, name: "Grape Juice", amount: 30, spendDate: "2025-10-12", category: "Food" },
   { id: 1, name: "Cinema", amount: 210, spendDate: "2025-10-16", category: "Entertainment" },
   { id: 1, name: "Java Programming book", amount: 242, spendDate: "2025-10-15", category: "Academic" },
   { id: 1, name: "Mango Juice", amount: 35, spendDate: "2025-10-16", category: "Food" },
   { id: 1, name: "Dress", amount: 2000, spendDate: "2025-10-25", category: "Cloth" },
   { id: 1, name: "Tour", amount: 2555, spendDate: "2025-10-29", category: "Entertainment" },
   { id: 1, name: "Meals", amount: 300, spendDate: "2025-10-30", category: "Food" },
   { id: 1, name: "Mobile", amount: 3500, spendDate: "2025-11-02", category: "Gadgets" },
   { id: 1, name: "Exam Fees", amount: 1245, spendDate: "2025-11-04", category: "Academic" }
]
```

Use _ExpenseEntryItemList_ component by passing the items through _items_ attributes.

```
ReactDOM.render(
   <React.StrictMode>
      <ExpenseEntryItemList items={items} />
   </React.StrictMode>,
   document.getElementById('root')
);
```

The complete code of _index.js_ is as follows −

```

import React from 'react';
import { createRoot } from 'react-dom/client';
import ExpenseEntryItemList  from './components/ExpenseEntryItemList'

const container = document.getElementById('root');
const root = createRoot(container);

const items = [
   { id: 1, name: "Pizza", amount: 80, spendDate: "2025-10-10", category: "Food" },
   { id: 1, name: "Grape Juice", amount: 30, spendDate: "2025-10-12", category: "Food" },
   { id: 1, name: "Cinema", amount: 210, spendDate: "2025-10-16", category: "Entertainment" },
   { id: 1, name: "Java Programming book", amount: 242, spendDate: "2025-10-15", category: "Academic" },
   { id: 1, name: "Mango Juice", amount: 35, spendDate: "2025-10-16", category: "Food" },
   { id: 1, name: "Dress", amount: 2000, spendDate: "2025-10-25", category: "Cloth" },
   { id: 1, name: "Tour", amount: 2555, spendDate: "2025-10-29", category: "Entertainment" },
   { id: 1, name: "Meals", amount: 300, spendDate: "2025-10-30", category: "Food" },
   { id: 1, name: "Mobile", amount: 3500, spendDate: "2025-11-02", category: "Gadgets" },
   { id: 1, name: "Exam Fees", amount: 1245, spendDate: "2025-11-04", category: "Academic" }
]

root.render(<ExpenseEntryItemList items={items}/>);
```

**ExpenseEntryItemList.css:**

Open _ExpenseEntryItemList.css_ and add style for the table.

```
html {
  font-family: sans-serif;
}
table {
   border-collapse: collapse;
   border: 2px solid rgb(200,200,200);
   letter-spacing: 1px;
   font-size: 0.8rem;
}
td, th {
   border: 1px solid rgb(190,190,190);
   padding: 10px 20px;
}
th {
   background-color: rgb(235,235,235);
}
td, th {
   text-align: left;
}
tr:nth-child(even) td {
   background-color: rgb(250,250,250);
}
tr:nth-child(odd) td {
   background-color: rgb(245,245,245);
}
caption {
   padding: 10px;
}
```

Start the application using npm command.

```
npm start
```

### Output

Finally, open the browser and enter _http://localhost:3000_ in the address bar and press enter.

| Item                  | Amount | Date            | Category      |
| --------------------- | ------ | --------------- | ------------- |
| Pizza                 | 80     | Sat Oct 10 2025 | Food          |
| Grape Juice           | 30     | Man Oct 12 2025 | Food          |
| Cinema                | 210    | Fri Oct 16 2025 | Entertainment |
| Java Programming book | 242    | Thu Oct 15 2025 | Academic      |
| Mango Juice           | 35     | Fri Oct 16 2025 | Food          |
| Dress                 | 2000   | Sun Oct 25 2025 | Cloth         |
| Tour                  | 2555   | Thu Oct 29 2025 | Entertainment |
| Meals                 | 300    | Fri Oct 30 2025 | Food          |
| Mobile                | 3500   | Mon Nov 02 2025 | Gadgets       |
| Exam Fees             | 1245   | Wed Nov 04 2025 | Academic      |

#  Understand `map()` with simple numbers

```
const numbers = [1, 2, 3];  
  
const result = numbers.map((num) => {  
  return num * 2;  
});
```

### What happens?

- First: num = 1 → returns 2
- Second: num = 2 → returns 4
- Third: num = 3 → returns 6

`map()` collects them automatically into:

```
[2, 4, 6]
```

👉 Important:  
The `return` runs 3 times.  
But `map()` stores everything in ONE new array.
# Now Apply That To Your Code

You wrote:

```
const lists = this.props.items.map((item) =>  
   <tr key={item.id}>  
      <td>{item.name}</td>  
      <td>{item.amount}</td>  
      <td>{new Date(item.spendDate).toDateString()}</td>  
      <td>{item.category}</td>  
   </tr>  
);
```

Let’s imagine you have only 2 items:

```
[  
  { name: "Pizza" },  
  { name: "Juice" }  
]
```
### What happens?

1️⃣ First item → returns:

```
<tr>Pizza</tr>
```

2️⃣ Second item → returns:

```
<tr>Juice</tr>
```

Now `map()` stores both in an array:

```
[  
  <tr>Pizza</tr>,  
  <tr>Juice</tr>  
]
```

That entire array is stored in:  lists
# Now Look at render()

```
return (  
   <tbody>  
      {lists}  
   </tbody>  
);
```

React sees this:

```
[  
  <tr>Pizza</tr>,  
  <tr>Juice</tr>  
]
```

React automatically prints them one after another.

So it becomes:

```
<tbody>  
  <tr>Pizza</tr>  
  <tr>Juice</tr>  
</tbody>
```
# Summary

Think like this:

```
map():  
   item1 → return row1  
   item2 → return row2  
   item3 → return row3  
  
map() collects:  
   [row1, row2, row3]  
  
render():  
   return <table>{[row1, row2, row3]}</table>
```

# React - Styling

In general, React allows component to be styled using CSS class through className attribute. Since, the React JSX supports JavaScript expression, a lot of common CSS methodology can be used. Some of the top options are as follows −

- **CSS stylesheet** − Normal CSS styles along with _className_
- **Inline styling** − CSS styles as JavaScript objects along with camelCase properties.
- **CSS Modules** − Locally scoped CSS styles.
- **Styled component** − Component level styles.
- **Sass stylesheet** − Supports Sass based CSS styles by converting the styles to normal css at build time.
- **Post processing stylesheet** − Supports Post processing styles by converting the styles to normal css at build time.

Let use learn how to apply the three important methodology to style our component in this chapter.

- CSS Stylesheet
- Inline Styling
- CSS Modules

## CSS Stylesheet

_CSS stylesheet_ is usual, common and time-tested methodology. Simply create a CSS stylesheet for a component and enter all your styles for that particular component. Then, in the component, use className to refer the styles.

Let us style our _ExpenseEntryItem_ component.

Open _expense-manager_ application in your favorite editor.

Next, open _ExpenseEntryItem.css_ file and add few styles.

```
div.itemStyle { 
   color: brown; 
   font-size: 14px; 
}
```

Next, open _ExpenseEntryItem.js_ and add _className_ to the main container.

```
import React from 'react';
import './ExpenseEntryItem.css';

class ExpenseEntryItem extends React.Component {
   render() {
      return (
         <div className="itemStyle">
            <div><b>Item:</b> <em>Mango Juice</em></div>
            <div><b>Amount:</b> <em>30.00</em></div>
            <div><b>Spend Date:</b> <em>2020-10-10</em></div>
            <div><b>Category:</b> <em>Food</em></div>
         </div>
      );
   }
}
export default ExpenseEntryItem;
```

Update index.js to include ExpenseEntryItem.

```
import React from 'react';
import { createRoot } from 'react-dom/client';
import ExpenseEntryItem  from './components/ExpenseEntryItem'

const container = document.getElementById('root');
const root = createRoot(container);

root.render(<ExpenseEntryItem />);
```

Next, serve the application using npm command.

```
npm start
```

Next, open the browser and enter _http://localhost:3000_ in the address bar and press enter.

![CSS Stylesheet](https://www.tutorialspoint.com/reactjs/images/css_stylesheet.jpg)

CSS stylesheet is easy to understand and use. But, when the project size increases, CSS styles will also increase and ultimately create lot of conflict in the class name. Moreover, loading the CSS file directly is only supported in Webpack bundler and it may not supported in other tools.

## Inline Styling

_Inline Styling_ is one of the safest ways to style the React component. It declares all the styles as _JavaScript objects_ using DOM based css properties and set it to the component through _style_ attributes.

Let us add inline styling in our component.

Open _expense-manager_ application in your favorite editor and modify _ExpenseEntryItem.js_ file in the src folder. Declare a variable of type object and set the styles.

```
itemStyle = {
   color: 'brown', 
   fontSize: '14px' 
}
```

Here, _fontSize_ represent the css property, font-size. All css properties can be used by representing it in _camelCase_ format.

Next, set _itemStyle_ style in the component using curly braces {} −

```
render() {
   return (
      <div style={ this.itemStyle }>
         <div><b>Item:</b> <em>Mango Juice</em></div>
         <div><b>Amount:</b> <em>30.00</em></div>
         <div><b>Spend Date:</b> <em>2020-10-10</em></div>
         <div><b>Category:</b> <em>Food</em></div>
      </div>
   );
}
```

Also, style can be directly set inside the component −

```
render() {
   return (
      <div style={
         {
            color: 'brown',
            fontSize: '14px'
         }         
      }>
         <div><b>Item:</b> <em>Mango Juice</em></div>
         <div><b>Amount:</b> <em>30.00</em></div>
         <div><b>Spend Date:</b> <em>2020-10-10</em></div>
         <div><b>Category:</b> <em>Food</em></div>
      </div>
   );
}
```

Now, we have successfully used the inline styling in our application.

Next, serve the application using npm command.

```
npm start
```

Next, open the browser and enter _http://localhost:3000_ in the address bar and press enter.

![Inline Styling](https://www.tutorialspoint.com/reactjs/images/inline_styling.jpg)

## CSS Modules

_Css Modules_ provides safest as well as easiest way to define the style. It uses normal css stylesheet with normal syntax. While importing the styles, CSS modules converts all the styles into locally scoped styles so that the name conflicts will not happen. Let us change our component to use _CSS modules_

Open expense-manager application in your favorite editor.

Next, create a new stylesheet, ExpenseEntryItem.module.css file under src/components folder and write regular css styles.

```
div.itemStyle {
   color: 'brown'; 
   font-size: 14px; 
}
```

Here, file naming convention is very important. React toolchain will pre-process the css files ending with _.module.css_ through _CSS Module_. Otherwise, it will be considered as a normal stylesheet.

Next, open _ExpenseEntryItem.js_ file in the _src/component_ folder and import the styles.

```
import styles from './ExpenseEntryItem.module.css'
```

Next, use the styles as JavaScript expression in the component.

```
<div className={styles.itemStyle}>
```

Now, we have successfully used the CSS modules in our application.

The final and complete code is −

```
import React from 'react';
import './ExpenseEntryItem.css';
import styles from './ExpenseEntryItem.module.css'

class ExpenseEntryItem extends React.Component {
   render() {
      return (
         <div className={styles.itemStyle} >
            <div><b>Item:</b> <em>Mango Juice</em></div>
            <div><b>Amount:</b> <em>30.00</em></div>
            <div><b>Spend Date:</b> <em>2020-10-10</em></div>
            <div><b>Category:</b> <em>Food</em></div>
         </div>
      );
   }
}
export default ExpenseEntryItem;
```

Next, serve the application using npm command.

```
npm start
```

Next, open the browser and enter _http://localhost:3000_ in the address bar and press enter.

![CSS Modules](https://www.tutorialspoint.com/reactjs/images/css_modules.jpg)
