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
```