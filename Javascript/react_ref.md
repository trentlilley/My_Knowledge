# Table of Contents

</br>

# Overview
These notes serve as my reference for the React framework.


**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some examples may come from various textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject. 
<br></br>

# Setup

## Quickstart for Existing Projects
- Below is a quick method to get started testing out React code in an existing web project
- All you need is a single html file and a single js file
- Note that this method is not suitable for production and is often not used in projects beyond simple tutorials.
- You simply need to include these script tags within the header tags of your html file

```html
<script crossorigin src="https://unpkg.com/react@17/umd/react.development.js"></script>

<script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
```
- If you want to use JSX (recommended to do so if you don't know what this is), you need to include the below script within the **head** tags as well

```html
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

- Afterwards you need to include the type=text/babel in any script tag you wish to use JSX in. This kind of tag is placed somewhere within the **body**.

- Recall that the src attribute references the javascript file name you want to write React in. In our case its index.js

```html
<script src="index.js" type="text/babel"><script>
```

- In case anythnig was unclear, a complete sample setup may look like this.

```html
<html>
    <head>
        <link rel="stylesheet" href="index.css">
        <script crossorigin src="https://unpkg.com/react@17/umd/react.development.js"></script>
        <script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
        <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    </head>
    <body>
        <div id="root"></div>
        <script src="index.js" type="text/babel"></script>
    </body>
</html>
```

## Create React App: New Projects
- Another remarkably easy way to configure a React development environment, suitable if you are starting from scratch.

- Great for beginners because a lot of configuration work involving the Babel transpiler and Webpack is already done for you, and your project will automatically recompile and run in response to your changes

- You need to ensure that you have node.js installed because you will need to use the npm package manager

> check if you have node installed by running `npm -v` in your terminal

- Open a code editor like visual studio and enter the following command to create a new React application. Replace my-app with whatever you wish to name your project

```bash
npx create-react-app my-app
```

- Ensure you are in the root directory and start the app using this command. If successful, it should open a webpage in your default browser with the react logo on it

```bash
npm start
```

- To really start from scratch, delete all of the contents within the src directory

<br></br>

# Basics and Syntax

## Starter Component
- In order for this to work properly, you need to have a tag in the body of your html with an id of "root"
- If you are using React version 17 or later, you do not have to explicitly import React or ReactDOM
- Every react component must have only one top level tag
- The render method is what renders our react code to our html
- It is customary for the fuction (component's) name to be capitalized. This naming convention is known as Pascal Case
- Comments must be placed within {} as shown below

```jsx
import React from "react"
import ReactDOM from "react-dom"

{/*A sample component*/}
function Title() = (
  return (
    <div>
      <h1>Title Goes Here</h1>
    </div>
  )    
)

ReactDOM.render(<Title />, document.getElementById("root"))
```

- Alternatively you can use a lambda assigned to a constant

```jsx
const Title = () => {
    return (
        <div>
          <h1>Title Goes Here</h1>
        </div>
    )
}
```

- You could also use a class however this is the most verbose option. It may be the most organized option for components that have complex behavior however.

```jsx
class Title extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <div>
              <h1>Title Goes Here</h1>
            </div>
        )
    }
}
```

## Nesting Components
- Components can be nested. The next two examples illustrated how this can be done.

- The below code does not use nesting. In the next example we will pull the header tags and all of its child tags out and put it into its own component

```jsx
{/*Page.jsx*/}
function Page() {
    return (
        <div>
            <header>
                <nav>
                    <img src="./logo.png" width="40px" />
                </nav>
            </header>
            <h1>Title Goes Here</h1>
            <ol>
                <li>item 1</li>
                <li>item 2</li>
            </ol>
            <footer>
                <p>Footer Goes Here</p>
            </footer>
        </div>
    )
}

ReactDOM.render(<Page />, document.getElementById("root"))
```

- This example uses nesting to make our components more concise and reusable. Generally, this is an advisable practice and in larger scale React applications, each component will be dedicated its own jsx file.

```jsx
{/*Page.jsx*/}
function Header() {
  return (
    <header>
      <nav>
        <img src="./logo.png" width="40px" />
      </nav>
    </header>
  )
}

function Page() {
    return (
        <div>
            <Header /> {/*Child Component*/}
            <h1>Title Goes Here</h1>
            <ol>
                <li>item 1</li>
                <li>item 2</li>
            </ol>
            <footer>
                <small>Footer Goes Here</small>
            </footer>
        </div>
    )
}

ReactDOM.render(<Page />, document.getElementById("root"))
```

## Organizing Components
- It is considered good pracice to give every component its own dedicated jsx file. In a web project, you may want to place all components within a directory titled "components".

- Following the previous example, we will now take out the header child component and put it into its own file named Header.jsx. We will then import it into Page.jsx

```jsx
{/*Header.jsx*/}
import React from "react"

function Header() {
  return (
    <header>
      <nav>
        <img src="./logo.png" width="40px" />
      </nav>
    </header>
  )
}

export default Header
```
>If you have multiple components or functions in this file, let's say you also had a `Footer` function, you would need to use `export { Header, Footer }` It may not be suitable to use `default` here because only one function can be declared default in a file

>Alternatively, you can apply export default to the function signature instead of creating a new export line like the following: `export default function Header`

- To import this component, we simply need to use an import statement within Page.jsx

```jsx
{/* Page.jsx */}
import Header from './Header';

function Page() {
    return (
        <div>
            <Header /> {/* Imported Component */}
            <h1>Title Goes Here</h1>
            <ol>
                <li>item 1</li>
                <li>item 2</li>
            </ol>
            <footer>
                <small>Footer Goes Here</small>
            </footer>
        </div>
    )
}
```

- In a simple but complete React application, our main **App component** may end up looking somehting like this

- This would be the only component that gets rendered to our DOM. The App component is treated as a "master" component that carries all other components

```jsx
{/* App.jsx */}
import React from "react";
import ReactDOM from "react-dom";
import Navbar from "./components/Navbar";
import Sidebar from "./components/Sidebar";
import Content from "./components/Content";
import Footer from "./components/Footer";
import "../styles/app.css";

function App() {
  return (
    <div>
      <Navbar />
      <Sidebar />
      <Content />
      <Footer />
    <div>
  )
}

ReactDOM.render(<App />, document.getElementById("root"))
```

## Incorporating JS Syntax

- In a React component, you are free to include any javascript syntax outside of the return statement. This is where you would typically initialize any new variables or objects.

- To add Javascript syntax within jsx however, you must surround the javascript with angle brackets. Content involving HTML tags in React is formally referred to as 'jsx' syntax.

```jsx
import React from "react"
import ReactDOM from "react-dom"

function App() {
    const date = new Date()
    
    return (
        <h1>It is currently about {date.getHours() % 12} !</h1>
    )
}

ReactDOM.render(<App />, document.getElementById("root"))
```


<br></br>

# Styling

## CSS

- Typically you will create your CSS in a separate file and import it into your react code using an import statement.

- If you want to give a tag a class, you need to use **className** rather than class as the attribute because class is a reserved word in Javascript

```jsx
// use className instead of class
<nav>
  <img src="./logo.png" className="nav-logo"/>
  <ul className="nav-items">
    <li>Pricing</li>
    <li>About</li>
    <li>Contact</li>
  </ul>
</nav>
```

- To style this navbar, create a css file. Below are some sample styles for our navbar, its logo, and its items

```css
/* styles.css */
.nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.nav-logo {
    width: 60px;
}

.nav-items {
    list-style: none;
    display: flex;
}

.nav-items > li {
    padding: 10px;
}

```

- In larger-scale projects, each react component should have its own dedicated css stylesheet. It is bad practice for all jsx files to share a global stylesheet. A global stylesheet should only be used for a few properties that will be global throughout the project.

- Instead of having a link to a global stylesheet in your html, you should import component-dedicated styleshseets into a jsx file using an import statement. This is all it takes to apply css to your components.

```jsx
// applying the styles
import "./navbar.css"

function Navbar() {
  <nav>
    //...
  </nav>
}
```

## Images
- Images must be imported into the component file and given a name. The name is then referenced within the src attribute of the img tag

```jsx
import image from "../images/logo.png"

export default function ImageContainer() {
  return (
    <img src={image} alt="logo" className="logo"/>
  )
}
```

<br></br>

# Props
- Props provide a way to pass "parameters" into react components, this is how you can give reused components different data

- Props are declared within the component's tag, you can declare as many props as you would like for any component

- Props that are not strings must be enclosed within angle brackets, this includes numbers, objects, booleans, and arrays

```jsx
/* App.jsx */
// a web app that displays two student contact cards
import React from "react"
import Contact from "./Contact"

function App() {
    return (
        <div className="contacts">
            <Contact 
                image="./images/stuB.png" 
                name="B"
                phone="(212) 555-1234"
                email="b@university.edu"
                year={10}
            />
            <Contact 
                img="./images/stuC.png"
                name="C"
                phone="(212) 555-2345"
                email="c@university.edu"
                year={12}
            />
        </div>
    )
}

export default App
```

- These props are then bundled into a single object that is passed into the component's signature, in most cases we name it `props` but we could use whatever name we like

- The individual properties can then be accessed using dot accessors

- Recall that accessing object properties using dot accessors is javascript syntax and as such it needs to be enclosed within angle brackets

```jsx
/* Contact.jsx */
import React from "react"

export default function Contact(props) {
    return (
        <div className="contact-card">
            <img src={props.img}/>
            <h3>{props.name} - {props.year}</h3>
            <div className="phone">
                <p>{props.phone}</p>
            </div>
            <div className="email">
                <p>{props.email}</p>
            </div>
        </div>
    )
}
```

- Destructuring the object right as it is passed into the function can save some clutter. The disadvantage is that in components with lots of variables it may become unclear which variables are props and which are not

- When using destructuring, the names must exactly match the prop names passed in

```jsx
/* Contact.jsx */
import React from "react"

export default function Contact({img, name, year, phone, email}) {
    return (
        <div className="contact-card">
            <img src={img}/>
            <h3>{name} - {year}</h3>
            <div className="phone">
                <p>{phone}</p>
            </div>
            <div className="email">
                <p>{email}</p>
            </div>
        </div>
    )
}
```

## Default Props
- We can assign default props to set values when they don't exist. These default values are automatically overriden when values are explicitly passed in

- Common scenario: shopping carts in online stores will have the items numbers set to 0 by default

```jsx
const Cart = (props) => {
    return (
        <div>
          <p>Items in cart: {props.count}</p>
        <div>
    )
};

Cart.defaultProps = {count: 0};
```







## Render Only if Prop Exists
- In React, there is no mandate that we must pass all of the props we include in the component's jsx code. In the previous student contact card example, maybe somone chooses not to have their image displayed

- Rather than render a broken image, we can choose not to include the img tag in the component entirely if no img prop is passed

- Surround the tag you want to conditionally render in angle brackets and '&&' it with the prop itself. This can be approximately translated to: if img prop exists, render the img tag.


```jsx
import React from "react"

export default function Contact({img, name, phone, email}) {
    return (
        <div className="contact-card">
            {img && <img src={img}/>} {/*conditional*/}
            <h3>{name} - {year}</h3>
            <div className="phone">
                <p>{phone}</p>
            </div>
            <div className="email">
                <p>{email}</p>
            </div>
        </div>
    )
}
```

- Why does this happen? As you may know, in most programming languages, the left side of the `&&` conditional statement is checked first. If this left side returns false, then the right side is never checked. This is why if img does not exist, the img tag is never rendered.

## Style Only if Prop Exists
- We can also style conditionally by surrounding a tag's inline styles with angle brackets

- In the example, we set the display of h3 to block if props.name exists and set it to none if props.name does not exist

```jsx
import React from "react"

export default function Message(props) {
    return (
        <div>
            <h3 style={{display: props.name ? "block" : "none"}}>Name: {props.name}</h3>
            <p>Text: {props.text}</p>
            <hr />
        </div>
    )
}
```

## HTML Elements as Props
- There is nothing that stops us from passing HTML elements (jsx elements) as data into components, this allows for some very powerful behavior.

- In this example, we create an array of h3 tags and pass the array as a prop called "data" into a componenent called Main
```jsx
/* App.js */

import React from "react";
import Main from "./Main";

export default function App() {
    const colors = [<h3>Red<h3>, <h3>Green<h3>, <h3>Blue<h3>]

    return (
        <Main data={colors}/>
    )
}
```

- This Main component will render all three of the h3 tags onto our webpage

```jsx
/* Main.jsx */
import React from "react";

export default function Main(props) {
    return (
        <div>{props.data}</div>
    )
}
```

- As another example, suppose we want to make a component that represents a typical "Work Experience" entry in a resume. Such an entry may have a variable number of bullet points so we do not want to write a component that confines the user to 3 bullet points. What if they want 4 or have room for only 2?

- The user can provide their data in the form of an array containing as many unordered list items (bullet points) as they want to insert into the component. This may be provided in a separate js file named "strings.js"

```jsx
/* strings.js */
export const experience1 = [
    "<li>Worked in marketing</li>",
    "<li>Collaborated with a team</li>",
    "<li>Increased sales by 20%</li>"
]

export const experience2 = [
    "<li>Tutored students in math</li>",
    "<li>Worked with kids age 12-18</li>"
    "<li>Created custom homework assignments</li>"
    "<li>Conducted weekly meetings</li>"
]
```

- We can write a component that takes this array as a prop named "bullets" and renders it to HTML

```jsx
/* Experience.jsx */
import React from "react";

export default function Experience(props) {
    <h2>{props.title}<h2><span><h4>{props.duration}</h4></span>
    <p>{props.location}</p>
    <ul>
        {props.bullets}
    </ul>
}
```
- The first experience component will render 3 bullets while the second will render 4 bullets

```jsx
/* App.jsx */
import React from "react";
import { experience1, experience2 } from "./strings.js"

export default App() {
    //other resume components...
    <Experience 
        title="Marketing Intern at CompanyName"
        duration="2015-2016"
        location="Westfold GA, USA"
        bullets={experience1}
    />
    <Experience 
        title="Tutor at SchoolName"
        duration="2013-2015"
        location="Westfold GA, USA"
        bullets={experience2}
    />
}
```
- An issue with the above example is that some of our data (only the bullets) are in our data holding file (strings.js) while the rest of the data is hardcoded into the App component. It is better design to have all data located within the dedicated data file (strings.js).

- A cleaner way to accomplish the above would be to use the **array mapping function** in Javascript 

- Instead of making multiple variables, one for each experience, we can instead create an array of objects so that all of our data is contained in a single variable

```jsx
/* strings.js */
export default [
    { //obj 1
        title: "Marketing Intern at CompanyName",
        duration: "2015-2016",
        location: "Westfold GA, USA"
        bullets: [
            "<li>Worked in marketing</li>",
            "<li>Collaborated with a team</li>",
            "<li>Increased sales by 20%</li>"
        ]

    },
    { // obj 2
        title: "Tutor at SchoolName",
        duration: "2013-2015",
        location: "Westfold GA, USA"
        bullets: [
            "<li>Tutored students in math</li>",
            "<li>Worked with kids age 12-18</li>"
            "<li>Created custom homework assignments</li>"
            "<li>Conducted weekly meetings</li>"
        ]        
    }
]
```

- Our component will look exactly the same as the previous example

```jsx
/* Experience.jsx */
import React from "react";

export default function Experience(props) {
    
    return (
        <h2>{props.title}<h2>
        <span>
            <h4>{props.duration}</h4>
        </span>
        <p>{props.location}</p>
        <ul>
            {props.bullets}
        </ul>
    )
}
```

- We must change how we pass in the props in our App component. We first use an array mapping function so that for each object in `data` (which is an array of objects), we create an `Experience` component with the necessary props passed in.

```jsx
/* App.jsx */
import React from "react";
import Experience from "./Experience"
import data from "./strings.js"

export default App() {
    // other resume components...
    // experienceList will be an array with 2 elements
    const experienceList = data.map(experience => {
        return <Experience
            title={experience.title}
            duration={experience.duration}
            location={experience.location}
            bullets={experience.bullets}
        /> 
    });

    // jsx
    return (
        <div>
            {experienceList}
        </div>
    )
}
```

- Our Experience component only takes four props, but what if we have a component that takes a hundred props. It would be very tedious to write out a hundred lines like title={experience.title} and so on.

- We can instead use the **spread operator**. This condenses passing our four props into one single line of code. We do not even need to change anything in the Experience component

```jsx
/* App.jsx */
import React from "react";
import Experience from "./Experience"
import data from "./strings.js"

export default App() {
    // other resume components...
    // experienceList will be an array with 2 elements
    const experienceList = data.map(experience => {
        return <Experience
            {...experience}
        /> 
    });

    // jsx
    return (
        <div>
            {experienceList}
        </div>
    )
}
```

# Events and State

- Events are activities that a user performs on your webpage. Examples of common events include clicking, mousing over, dragging, and scrolling.

- Typically, we use these events to change the state of our application. Examples of state changes include changing the values of variables, changing the visibility of elements, extracting information from forms and updating the textual contents of jsx elements etc.

- As a real world example: clicking the '+' button on a menu item in a fast-food ordering application should increment the number of selections by 1.

## Updating Variables
- In the example below, we have an array of data. Each item in the array is surrounded with paragraph tags and appended to our jsx. We want to create an app that has a button that allows us to add elements to this array and display the updated contents of the array.

- We set up an `onClick` attribute for our button. This configures a basic event listener that calls the function addItem whenever the button is clicked

```jsx
function App() {
    const thingsArray = ["Thing 1", "Thing 2"]
    const thingsElements = thingsArray
        .map(thing => <p key={thing}>{thing}</p>)
    
    function addItem() {
        console.log(thingsArray)
    }
    
    return (
        <div>
            <button onClick={addItem}>Add Item</button>
            {thingsElements}
        </div>
    )
}
```

- How can we make the button append another element to our thingsArray (Thing 3, Thing 4, etc) on click? We can do so by adding some basic javascript into the addItem method

```jsx
function App() {
    const thingsArray = ["Thing 1", "Thing 2"]
    const thingsElements = thingsArray
        .map(thing => <p key={thing}>{thing}</p>)
    
    function addItem() {
        const newThingText = `Thing ${thingsArray.length + 1}`
        thingsArray.push(newThingText)
        console.log(thingsArray)
    }
    
    return (
        <div>
            <button onClick={addItem}>Add Item</button>
            {thingsElements}
        </div>
    )
}
```

- This will add new items to the array, however, as of now, React does not know to render this updated array and will keep displaying the old two-item array to the webpage



