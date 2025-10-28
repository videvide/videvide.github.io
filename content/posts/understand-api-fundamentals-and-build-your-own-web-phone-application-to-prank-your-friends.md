---
date: '2025-10-28T16:07:06+02:00'
title: 'Understand API Fundamentals and Build Your Own Web Phone Application to Prank Your Friends 🤡'
tags: ["api", "rest", "jwt", "javascript", "node", "vite", "express", "mongodb", "crud"]
categories: ["full-stack"]
---

**Disclaimer:** The purpose of this toy project is to learn the fundamentals of APIs, not to build a production ready web application. We will use JWTs without refresh tokens and store them inside localstorage -- But we know it is bad practise, and to implement a more secure approach is out of the scope for this guide. 

**Advice:** Use LLMs and search engines to expand on topics and do more research, or if you just get stuck on a command.

### Some of the technologies we will use:

- [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Node.js](https://nodejs.org/en)
- [Express](https://expressjs.com/)
- [Vite](https://vite.dev/)
- JWT (using [this](https://github.com/panva/jose) package)
- [REST](https://en.wikipedia.org/wiki/REST) API Architecture
- [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)

## API?

![alt text](https://www.akamai.com/site/en/images/article/2023/how-a-web-api-works.png)

API stands for "Application Programming Interface" and is what we use to communicate with other applications. Most large scale web application such as Google and Meta have their own APIs. 

Through these APIs we can interact with the underlying web applications. The developers of these underlying web applications choose what functionality and data they want to expose, and how, which then let other developers like us interact with it in a structured, and hopefully documented way.

APIs use the Server-Client architecture, having the client (browsers, native applications, or other web applications and software) use the server (that serves the API). 

Let's do a quick example of an API request. We use an API for random users: [https://randomuser.me/](https://randomuser.me/). Paste the following code into your browsers console and press enter:
```js
let result = await fetch("https://randomuser.me/api/");
let json = await result.json();
json.results[0];
```

This will fetch a random user and print the result, and that's how easy it can be to consume an API.

```js
{
  "gender": "female",
  "name": {
    "title": "Ms",
    "first": "Shraddha",
    "last": "Acharya"
  },
  "location": {
    "street": {
      "number": 123,
      "name": "Unknown Street"
    },
    "city": "Patiala",
    "state": "Bihar",
    "country": "India",
    "postcode": 52659
  },
  "email": "shraddha.acharya@example.com",
  "login": {
    "uuid": "c0dd02b1-7d95-4ad7-866f-05f58816588a",
    "username": "yellowbear416",
    "password": "gladiato",
    "salt": "2MvN1BVA",
    "md5": "5c08ed83bbe5900bf7357c347601f779",
    "sha1": "b5f07c04fee266404fcd34136a6343eb83dd51c8",
    "sha256": "49ee4ac1129e22e04c84ed01711af5c2c77186ea88292a9dbf01c6f0b3fe2a6f"
  },
  "dob": {
    "date": "1997-12-07T15:27:17.841Z",
    "age": 27
  },
  "registered": {
    "date": "2009-06-26T05:27:18.844Z",
    "age": 16
  },
  "phone": "8043556361",
  "cell": "9396809804",
  "id": {
    "name": "UIDAI",
    "value": "889362812163"
  },
  "picture": {
    "large": "https://randomuser.me/api/portraits/women/53.jpg",
    "medium": "https://randomuser.me/api/portraits/med/women/53.jpg",
    "thumbnail": "https://randomuser.me/api/portraits/thumb/women/53.jpg"
  },
  "nat": "IN"
}
```

Now you made your first API request, congratulations!

In this guide we will both implement our own API using the JavaScript library ```express``` and also interact with a third party API from [46elks.se](https://46elks.se/) to create a "web-phone" application.

To make things a bit more interesting we will usa a decoupled architecture, meaning the client and API will be running on two different ports.

## Project description:

This application is a "web-phone", a simple web application with simple phone functionalities like storing contacts, sending SMS, and making phone calls. 

Users will be able to sign up and create a phone book with their contacts, then make phone calls, and send SMS with help of a third party API provided by [46elks](https://46elks.se/). 

To follow along you need credits at 46elks.se. You can [contact them](https://46elks.se/support) and ask for some.

## Project structure:

To create a [decoupled application](https://www.google.com/search?q=decoupled+architecture&oq=dec&gs_lcrp=EgZjaHJvbWUqBggAEEUYOzIGCAAQRRg7MgYIARBFGDsyBggCEEUYOTINCAMQABiDARixAxiABDIGCAQQRRg8MgYIBRBFGD0yBggGEEUYPTIGCAcQRRhB0gEIMTA2M2owajSoAgCwAgE&sourceid=chrome&ie=UTF-8) we more or less need two applications, one for our backend logic (API), and one for our frontend logic (Client). This is different from the prior architecture (SPA) we used in the recent JavaScript guide, [building a toy project.](https://videvide.github.io/posts/learn-about-javascript-and-its-ecosystem-while-building-a-toy-project/)

Using a decoupled architecture comes with its own quirks. For one, handling authentication in a secure manner can be challenging. This is something beyond the scope of this project, do not worry!

Let's start with scaffolding the project. 

I am on a Mac machine, and your shell code might be different:

```bash
# create the top-level project directory
mkdir web-phone; cd web-phone;
# create the api project and set type=module, then change directory to top-level one
mkdir backend; cd backend; npm init -y > /dev/null 2>&1; npm pkg set type=module; cd ..
# do the same for the frontend
mkdir frontend; cd frontend; npm init -y > /dev/null 2>&1; npm pkg set type=module; cd ..
```

Your project should look like this:
```bash
├── backend
│   └── package.json
└── frontend
    └── package.json
```

Next we install the dependencies for our backend and frontend:
```bash
# change directory to backend, install dependencies, change directory back to top-level
cd backend; npm i express cors mongodb jose; cd ..
# do the same for the frontend
cd frontend; npm i -D vite; cd ..
```

Then add some files and directories, your project should look like this:
```bash
├── .gitignore
├── backend
│   ├── controllers
│   │   ├── phoneBookController.js
│   │   └── userController.js
│   ├── middlewares
│   │   └── auth.js
│   ├── package.json
│   ├── server.js
│   └── services
│       ├── db.js
│       └── jwt.js
└── frontend
    ├── index.html
    ├── package.json
    └── src
        └── main.js
```

Make sure to include correct directories, files, and extensions in your .gitignore file.

## Writing the first lines of backend code:

We begin writing some backend code to hold the express server and supply routes to create users. We also write the first JWT logic to be able to log users in.

Write this code into your server.js file:
```js
import express from "express"
import cors from "cors"
// we import functions from this module that we soon will write
import {signup, login, logout} from "./controllers/userController.js"

const port = 3000
const app = express()

app.use(cors())
app.use(express.json())

// route to create users 
app.post('/signup', signup)

// route to login users
app.post('/login', login)

// route to logout users
// this is only for future purpose
// "logout" happens in client with current setup (JWT / localstorage)
app.post('/logout', logout)

app.listen(port, () => {
  console.log(`API listening on port ${port}`)
})
```

Each route is using a function imported from the userController.js module, let's write the logic inside that file:
```js
// import these functions, we will create them in a while
import { userCollection } from "../services/db.js"
import { generateToken } from "../services/jwt.js"

// function to create a user 
export async function createUser (req, res) {
    // extract the data from the request
    const formData = req.body
    // store it in variables for easier handling
    const email = formData.email
    const password = formData.password
    // try save a user to the database
    try {
        await userCollection.insertOne({
            email: email,
            password: password
        })
    } 
    // catch any errors 
    catch (e) {
        console.log(e)
    }
    // return a response with the generated token
    return res.send(generateToken({
        email: email,
        role: "user"
    }))
}
```

Now we begin write the logic to our db.js and jwt.js files. 

First of all, make sure you have a [MongoDB server](https://www.mongodb.com/try/download/community) instance installed and running on your machine. We need this for the mongodb Node client to interact with. To check this on a Mac machine, run the following in your terminal:
```bash
# command to list services running through homebrew package manager
brew services list
# output list of all currently running services, there might be more than this one 
Name                  Status  User File
mongodb-community@8.0 started vide ~/Library/LaunchAgents/homebrew.mxcl.mongodb-community@8.0.plist
```

You also need to create a database for the project, I am using [mongosh](https://www.mongodb.com/docs/mongodb-shell/) to connect to the running service using the default url:
```bash
# command to "jump in" to the database server
mongosh "mongodb://localhost:27017"
# to create a new database
use webphone
# then exit
exit
```

Then add this to your db.js file to perform a Create operation to the database: 
```js 
import { MongoClient } from "mongodb"

// set the url to the locally running mongodb instance
const url = "mongodb://localhost:27017";
// initiate the client 
const client = new MongoClient(url)
// specify the name of the newly created database
const dbName = "webphone";
// connect to the database server
await client.connect()
// connect to the database 
const db = client.db(dbName)
// export the user collection we need to perform CRUD
export const userCollection = db.collection("users")
```

Add this to your jwt.js file to handle the JWT logic:
```js
import * as jose from "jose"

// create a secret key to sign our JWTs
// we can also save it to .env file or equal
const secret = new TextEncoder().encode(
    "GIo1kkI927PuvzXu7oBC5XzDdS2HIYRqan2zeEnx5CfJcih9UwfUyUbGUIyufztex"
)
// specify a cryptographic algorithm to sign with
const alg = "HS256"

// function to generate and return a signed jwt-token with provided data
export async function generateToken(data) {
  return await new jose.SignJWT(data)
  .setProtectedHeader({ alg })
  .sign(secret)
}

// function to validate jwt-token
export async function validateToken(token) {
  try {
    // try to validate
    const { payload } = await jose.jwtVerify(token, secret)
    // return the valid data, same as saved from form
    return payload
  } catch (e) {
    // log error 
    console.log(error)
  }
}
```

## Writing the first lines of frontend code:

Add this to your index.html file. It will create a simple form to take in user data to register a user. We will later write the JavaScript logic to handle the form submission, sending the data to the backend API.

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>client</title>
  </head>
  <body>
    <div>
    <h1>Create User</h1>
    <form action="post" id="create-form">
        <label for="email-create">Email</label>
        <input type="email" name="email-create" id="email-create">
        <label for="password-create">Password</label>
        <input type="password" name="password-create" id="password-create">
        <input type="submit" value="Create User">
    </form>
    </div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>

```

Now add this to your main.js file to handle form submission: 
```js 
// get the form element
const createForm = document.getElementById("create-form")
const loginForm = document.getElementById("login-form")

// add eent listener that listens to form submission
createForm.addEventListener("submit", async (e) => {
  // prevent default form submission behavior handled by browser
  e.preventDefault()
  // get the values from the form fields
  let email = document.getElementById("email-create").value
  let password = document.getElementById("password-create").value
  // use fetch to call the backend API
  const res = await fetch("http://localhost:3000/create-user", {
    // make sure method is post 
    method: "post",
    headers: {
      // and content type json
      "content-type": "application/json"
    },
    // stringify the form values
    body: JSON.stringify({
      "email": email,
      "password": password
    })
  })
  // the response is our signed JWT token 
  const token = await res.text()
  // save it to localstorage to use in following requests 
  localStorage.setItem("token", token)
})
```

There is the first basic (and very insecure) approach to our user authentication. Next we add the logic to login and logout our user, as well as the CRUD operations for our phone book functionality. Later we add the phone functions, including calling and sending SMS. 

## Writing further authentication logic:

coming soon...