# MERN Stack on Ubuntu 20.04 

The MERN stack is an acronym for MongoDB, Express, React / Redux, and Node.js. The MERN stack is one of the most popular JavaScript stacks for building modern single-page web applications (SPA).

In this tutorial, we'll build a simple ToDo application that uses a RESTful API.

### Prerequisites
1. An Ubuntu VM (In this tutorial we're using an Ubuntu 20.04 on GCP VM Instance)\
   
> If you're interested in going the GCP route. This short tutorial would help get you started <https://cloud.google.com/compute/docs/instances/create-start-instance>

This tutorial is broadly divided into 4 major categories

1. Initiating up the Backend
2. Installing MongoDB
3. Hacking together the Frontend
4. Tying everything together

## Initiating The Back-end Project

The back-end will comprise HTTP endpoints to cover the following use cases:

- Create a new todo item in the database by sending an HTTP **POST** request
- Retrieve the complete list of available todo items by sending an HTTP **GET** request
- Delete an existing todo item in the database by sending an HTTP **DELETE** request
  
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
Output:

v12.21.0
```

`~$ npm -v`
```
Output:

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

>Hint: You can use the Editor feature in GCP, by clicking "Open Editor" in the cloud shell. Instead of using vi or nano on the terminal.

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/editor.PNG)

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

After following the process, you should end up with something like this:

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/firewallrule.PNG)

After that is done, you can visit your <http://Public-IP:5000> or better still, since we're using the Cloud Shell on our VM, you can click on "web preview"

1[](https://github.com/Arafly/Mern_Stack/blob/master/assets/webpreview.PNG)

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Express.PNG)

Remember we said earlier that what we what our back-end to cover are these use cases:

- Create a new todo item in the database by sending an HTTP **POST** request
- Retrieve the complete list of available todo items by sending an HTTP **GET** request
- Delete an existing todo item in the database by sending an HTTP **DELETE** request

For each of these tasks, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes. You can decide to use the terminal or your editor (I'm using the Cloud Shell Editor in my case).

In the *routes* folder, create a *backend.js* file and paste in the following codes:

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

## Installing MongoDB

Now that we’ve managed to set up a basic Node.js / Express server we’re ready to continue with the next task: setting up the MongoDB database.

We'll define a *models* to define the database schema.

Remember we installed "mongoose" earlier? This will help create a Schema and a model.

Now, create a new folder named *models*. Inside the models folder, create a file and name it *todo.js*. Then paste the code below in the file:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

We've got to replace the placeholder code that we placed in our *backend.js* earlier. Replace everything in the backend.js file with the following code, so as to enable it fetch stuff from Mongo

```
const express = require("express");
const router = express.Router();
const Todo = require("../models/todo");

router.get("/todos", (req, res, next) => {
  //this will return all the data, exposing only the id and action field to the client
  Todo.find({}, "action")
    .then((data) => res.json(data))
    .catch(next);
});

router.post("/todos", (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then((data) => res.json(data))
      .catch(next);
  } else {
    res.json({
      error: "The input field is empty",
    });
  }
});

router.delete("/todos/:id", (req, res, next) => {
  Todo.findOneAndDelete({ _id: req.params.id })
    .then((data) => res.json(data))
    .catch(next);
});

module.exports = router;

```

At this point we need DB to be hosted somewhere, which is where **mLab** comes into the picture.
mLab provides MongoDB database as a service solution (DBaaS).You will need to sign up for a shared clusters free account, which is ideal for our use case.

- Sign up <https://www.mongodb.com/atlas-signup-from-mlab>
- Follow the sign up process, - Select GCP as the cloud provider, and choose a region near you.

There are a few things you must do to allow you connect to your hosted DB on mLab.
First on the Network access tab you must allow access to the MongoDB database from anywhere 
*image Mongo network access

>IMPORTANT NOTE In the image below, make sure you change the time of deleting the entry from 6 Hours to 1 Week

Next is to create a MongoDB database and collection inside mLab

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Mongo-create-DB-1.png)

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Mongo-create-DB-2.png)

Additionally, add the connection string to access the database you just created from inside your app, just as below:

`DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`

Ensure to update <username>, <password>, <network-address> and <database> according to your setup

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Mongo-connect1.png)

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Mongo-connect2.png)

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Mongo-connect3.png)

Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below.

```
const express = require("express");
const bodyParser = require("body-parser");
const mongoose = require("mongoose");
const routes = require("./routes/api");
const path = require("path");
require("dotenv").config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose
  .connect(process.env.DB, { useNewUrlParser: true })
  .then(() => console.log(`Database connected successfully`))
  .catch((err) => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header(
    "Access-Control-Allow-Headers",
    "Origin, X-Requested-With, Content-Type, Accept"
  );
  next();
});

app.use(bodyParser.json());

app.use("/api", routes);

app.use((err, req, res, next) => {
  console.log(err);
  next();
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});

```

Start your server using the command:

`node index.js`

```
Output:

araflyayinde@cloudshell:~/todoly$ node  index.js
Keeping my eyes peeled on port 5000

Database connected successfully
```

## Testing Backend Code without Frontend using RESTful API
Even we've written the backend part of our To-Do application, and configured a database, we're left with the frontend, which we'll create with ReactJS. However, usually during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project, we will use Postman to test our API. Click Install Postman <https://www.getpostman.com/> to download and install postman on your machine (I'll be using the browser-enabled version of Postman).

You should test all the API endpoints and make sure they are working. For the endpoints that require body, we'd send JSON back with the necessary fields since it’s what we setup in our code.

Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. 
This request sends a new task to our To-Do list so the application could store it in the database.

>Note: make sure your set header key Content-Type as application/json

! In my case, due to the more secured nature of the VM I'm on and also using POSTMAN client to try to access Google Cloud API’s. I had to generate a token so that I could authenticate to google cloud. This article here helped alot <https://pnatraj.medium.com/google-cloud-api-with-postman-f4cf070e665f>

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Postrequest.PNG)

## Hacking together the Frontend

It's time to create a UI to interact with the ToDO app via API. To start out with the frontend of the To-do app, we will use the *create-react-app* command to scaffold our app.

In a terminal, which is the same root directory as your backend code, which is the *todoly* directory, run:

`$ npx create-react-app client`

This will create a new folder in your Todo directory called *client*, where you will add all the react code.

You'll end up with a project structure like this:

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/projstructure.PNG)

In Todo folder open the package.json file. Change the "start" code blockand replace with the code below.

```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

Also, configure Proxy in package.json. Change directory to ‘client’. Open the package.json file and add the key value pair in the package.json file

`"proxy": "http://localhost:5000"`

The whole purpose of adding the proxy configuration is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, ensure you are inside the Todo directory, and simply do:

`npm run dev`

Your app should open and start running on localhost:3000

> Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Firewall rule.

### Creating your React Components

Go to the client folder and enter into src, create a new foler name "components".

Enter into components and create the three files -  Input.js, ListTodo.js and Todo.js.

![](https://github.com/Arafly/Mern_Stack/blob/master/assets/Components.PNG)

Next enter the Input.js file. Copy and paste the follwoing React code:

```
import React, { Component } from "react";
import axios from "axios";

class Input extends Component {
  state = {
    action: "",
  };

  addTodo = () => {
    const task = { action: this.state.action };

    if (task.action && task.action.length > 0) {
      axios
        .post("/api/todos", task)
        .then((res) => {
          if (res.data) {
            this.props.getTodos();
            this.setState({ action: "" });
          }
        })
        .catch((err) => console.log(err));
    } else {
      console.log("input field required");
    }
  };

  handleChange = (e) => {
    this.setState({
      action: e.target.value,
    });
  };

  render() {
    let { action } = this.state;
    return (
      <div>
        <input type="text" onChange={this.handleChange} value={action} />
        <button onClick={this.addTodo}>add todo</button>
      </div>
    );
  }
}

export default Input;

```

You must have noticed we defined 'axios' on Line 2.

`import axios from "axios";`

To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

Move to the src folder using your terminal.

`$ npm install axios`

Go to ListTodo.js in your components directory and also paste the following React code.

```
import React from "react";

const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {todos && todos.length > 0 ? (
        todos.map((todo) => {
          return (
            <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
              {todo.action}
            </li>
          );
        })
      ) : (
        <li>No todo(s) left</li>
      )}
    </ul>
  );
};

export default ListTodo;
```

Also do the same for your Todo.js and paste the following:

```
import React, { Component } from "react";
import axios from "axios";

import Input from "./Input";
import ListTodo from "./ListTodo";

class Todo extends Component {
  state = {
    todos: [],
  };

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios
      .get("/api/todos")
      .then((res) => {
        if (res.data) {
          this.setState({
            todos: res.data,
          });
        }
      })
      .catch((err) => console.log(err));
  };

  deleteTodo = (id) => {
    axios
      .delete(`/api/todos/${id}`)
      .then((res) => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch((err) => console.log(err));
  };

  render() {
    let { todos } = this.state;

    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
```

Proceed to your App.js and paste in the following 

```
import React from "react";

import Todo from "./components/Todo";
import "./App.css";

const App = () => {
  return (
    <div className="App">
      <Todo />
    </div>
  );
};

export default App;

```

As well as in the src directory open the App.css and paste:

```
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

Next is in the src directory open the index.css

```
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

Using your terminal now. Return to the todoly directory

`cd ../..`

When you are in the Todo directory run:

`npm run dev`

If all went well when saving all these files, our Todoly app should be ready and fully functional with the functionality discussed earlier: creating a task, deleting a task and viewing all your tasks, integrated.
