# MERN Stack on Ubuntu 20.04 

The MERN stack is an acronym for MongoDB, Express, React / Redux, and Node.js. The MERN stack is one of the most popular JavaScript stacks for building modern single-page web applications (SPA).

In this tutorial, we'll build a simple ToDo application that uses a RESTful API.

### Prerequisites
1. An Ubuntu VM (In this tutorial we're using an Ubuntu 20.04 on GCP VM Instance)\
   
> If you're interested in going the GCP route. This short tutorial would help get you started <https://cloud.google.com/compute/docs/instances/create-start-instance>

This tutorial is broadly divided into 4 major categories

1. Setting up the Backend
2. Setting up MongoDB
3. Hacking together the Frontend
4. Tying everything together

## Initiating The Back-end Project

The back-end will comprise HTTP endpoints to cover the following use cases:

- Retrieve the complete list of available todo items by sending an HTTP **GET** request
- ~~Retrieve a specific todo item by sending HTTP GET request and provide the specific todo ID in addtion~~
- Create a new todo item in the database by sending an HTTP POST request
- Update an existing todo item in the database by sending an HTTP POST request
  
Update ubuntu

`sudo apt update`

Upgrade ubuntu

`sudo apt upgrade`

Lets get the location of Node.js software from Ubuntu repositories.

`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

Install Node.js

`sudo apt-get install -y nodejs`

`~$ node --version`

```
Output
v12.21.0
```

`~$ npm -v`
```
Output
6.14.11
```

### App Setup
Create a new directory for the To-Do project, which we'd be naming "Todoly":

`mkdir todoly`

Enter into the newly created directory and initialize Node and npm into your project:

`npm init -y`

```
Output:

{
  "name": "todoly",
  "version": "1.0.0",
  "description": "A simple To-Do App",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "node",
    "mern",
    "ubuntu"
  ],
  "author": "Molo",
  "license": "ISC"
}
```

By running that npm command, a package.json will be automatically created. It stores up-to-date information about your project.

With the package.json file available in the project folder we’re ready to add some dependencies to the project:

`$ npm install express dotenv mongoose`

Let’s take a quick look at some of the installed packages:

- express: Express is a fast and lightweight web framework for Node.js. Express is an essential part of the MERN stack.
- dotenv: an environment file to help store sensitive data concerning our project
- mongoose: A Node.js framework which lets us access MongoDB in an object-oriented way.
- 
Finally we need to make sure to install a global package by executing the following command:

`$ npm install -g nodemon`

- Nodemon is a utility that will monitor for any changes in your source and automatically restart your server. We’ll use nodemon when running our Node.js server in the next steps.

Inside of the todoly project folder enter into the index.js and insert the following basic Node.js / Express server implementation:

```
const express = require("express");
require("dotenv").config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header(
    "Access-Control-Allow-Headers",
    "Origin, X-Requested-With, Content-Type, Accept"
  );
  next();
});

app.use((req, res, next) => {
  res.send("Welcome to Express");
});

app.listen(port, () => {
  console.log(`Keeping my eyes peeled on port ${port}`);
});

```

Hint: You can use the Editor feature in GCP, by clicking "Open Editor" in the cloud shell. Instead of using vi or nano on the terminal.
*image

With this code we’re creating an Express server and making the server listening on port 5000.

Start the server by using nodemon:

`$ nodemon index.js`

```
Output:

~/todoly$ nodemon index.js
[nodemon] 2.0.7
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node index.js`
Keeping my eyes peeled on port 5000
```

Now open up inbound traffic on port 5000 for our VM. 
Confused about how to do that? This article really helps <https://www.cloudsavvyit.com/4932/how-to-open-firewall-ports-on-a-gcp-compute-engine-instance/>

## Mongo

```
araflyayinde@cloudshell:~/todoly$ node  index.js
Keeping my eyes peeled on port 5000

Database connected successfully
```

### Testing Backend Code without Frontend using RESTful API


## Frontend

```
 npx create-react-app client


cd client
Open the package.json file
vi package.json
Add the key value pair in the package.json file "proxy": "http://localhost:5000".

Move to the src folder

cd ..
Move to clients folder

cd ..
Install Axios

$ npm install axios
Go to ‘components’ directory
```

```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```

```
vi App.js
Copy and paste the code below into it

import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```

```
In the src directory open the App.css

vi App.css
Then paste the following code into App.css:

.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```

```
In the src directory open the index.css

vim index.css
Copy and paste the code below:

body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```