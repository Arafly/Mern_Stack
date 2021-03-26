Create a virtual machine instance
In the VM instances section, click the Create button.

Enter a name for this instance.

Choose a region and a zone for this instance.

In the Machine type menu, select micro, which specifies a lower-cost machine type. (Learn more about pricing.)

In the Firewall selector section, select Allow HTTP traffic. This opens port 80 (HTTP) to access the app.

Click Create to create the instance.

Open the Cloud Shell
Open Cloud Shell by clicking the  Activate Cloud Shell button in the navigation bar in the upper-right corner of the console.

Connect to the instance
Connect to the VM using SSH. If this is your first time using SSH from Cloud Shell, you will need to create a private key. Enter the zone and name of the instance you created.

gcloud compute --project \
    "arcane-splicer-308504" ssh \
    --zone [vm-zone] [vm-name]
Replace [vm-zone] and [vm-name] with the zone and name of the instance that you created.

If this is your first time using SSH from Cloud Shell, follow the instructions to create a private key.

It may take several minutes for the SSH key to propagate.

```
araflyayinde@mernstack:~$ node --version
v12.21.0
araflyayinde@mernstack:~$ npm -v
6.14.11

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
araflyayinde@cloudshell:~/todoly (arcane-splicer-308504)$ npm install express
added 50 packages, and audited 51 packages in 2s
found 0 vulnerabilities

araflyayinde@cloudshell:~/todoly (arcane-splicer-308504)$ ls
index.js  node_modules  package.json  package-lock.json
araflyayinde@cloudshell:~/todoly (arcane-splicer-308504)$ npm install dotenv
added 1 package, and audited 52 packages in 1s
found 0 vulnerabilities
araflyayinde@cloudshell:~/todoly (arcane-splicer-308504)$
```

## Backend Codes

```
const express = require ('express');
const todoRoutes = express.todoRoutes();

todoRoutes.get('/todos', (req, res, next) => {

});

todoRoutes.get('/:id', (req, res, next) => {
    let id = req.params.id;
    // Todo.findById(id, (err, todo)=> {
    //     res.json(todo);
    // });
});

todoRoutes.post('/todos', (req, res, next) => {

});

todoRoutes.delete('/todos/:id', (req, res, next) => {

})

module.exports = todoRoutes;

Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
$ npm install nodemon --save-dev
In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

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