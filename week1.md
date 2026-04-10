# 📘 WEEK 1: WEB FOUNDATIONS, MERN OVERVIEW & PROJECT SETUP

---

## 🎯 Week Objective

By the end of this week, students will:

* Understand how web applications work end-to-end
* Learn essential web development terminology
* Understand the MERN stack at a high level
* Set up a complete development environment
* Build and run a basic REST API
* Test APIs using professional tools
* Understand project requirements
* Initialize a scalable project structure

---

# 🟥 1. WEB DEVELOPMENT FUNDAMENTALS

---

## 🔴 What is Web Development?

Web development is the process of building applications that run on the internet.

These applications allow users to:

* Interact with systems
* Send and receive data
* Perform real-world operations

---

## 🔴 Core Components of a Web Application

Every web application consists of:

### 1. Frontend (Client Side)

* User Interface (UI)
* Handles user interaction
* Sends requests to backend

---

### 2. Backend (Server Side)

* Business logic
* Authentication
* Data processing

---

### 3. Database

* Stores application data
* Ensures persistence

---

## 🔴 System Flow (MOST IMPORTANT CONCEPT)

```text
User → Frontend → Backend → Database
                     ↓
                 Response
```

👉 This flow must be understood clearly before moving forward

---

## 🔴 Important Terminologies

| Term            | Meaning                       |
| --------------- | ----------------------------- |
| Website         | Static content                |
| Web Application | Interactive system            |
| Web Design      | UI/UX                         |
| Web Development | Logic + system                |
| Full Stack      | Frontend + Backend + Database |

---

## 🔴 HTTP (Communication Protocol)

HTTP is the protocol used for communication between client and server.

---

### 🔹 Request–Response Cycle

1. User performs action
2. Request sent to server
3. Server processes request
4. Response returned

---

### 🔹 HTTP Methods

| Method | Purpose       |
| ------ | ------------- |
| GET    | Retrieve data |
| POST   | Create data   |
| PUT    | Update data   |
| DELETE | Remove data   |

---

## 🔴 What is an API?

API (Application Programming Interface) allows communication between systems.

Example:

```text
GET /users
POST /login
```

---

## 🔴 What is JSON?

JSON is the standard format used to send data.

```json
{
  "name": "Mustafa",
  "email": "mustafa@gmail.com"
}
```

---

# 🟥 2. MERN STACK OVERVIEW

---

## 🔴 What is MERN?

MERN is a full-stack JavaScript technology stack:

* MongoDB → Database
* Express → Backend framework
* React → Frontend
* Node.js → Runtime

---

## 🔴 How MERN Works

```text
React → Express API → Node Logic → MongoDB
```

---

## 🔴 Why MERN?

* Single language (JavaScript)
* Fast development
* Scalable architecture
* Industry demand

---

# 🟥 3. DEVELOPMENT ENVIRONMENT SETUP

---

## 🔴 Required Tools

### 🧩 Code Editor

* Primary: VS Code

### Alternatives:

* Cursor (AI-powered editor)
* Antigravity

---

### ⚙️ Runtime

* Node.js

---

### 🗄️ Database

* MongoDB

---

### 🔄 Version Control

* Git
* GitHub

---

### 📦 Package Managers

* npm
* yarn

---

## 🔴 Installation Verification

Students must run:

```bash
node -v
npm -v
git --version
```

---

# 🟥 4. BUILDING FIRST BACKEND API

---

## 🔴 What is a REST API?

A REST API allows communication using HTTP methods.

---

## 🔴 Create Express Server

```js
const express = require('express');
const app = express();

app.use(express.json());

app.get('/', (req, res) => {
  res.send('API is running');
});

app.get('/users', (req, res) => {
  res.json([{ name: "Mustafa" }]);
});

app.post('/users', (req, res) => {
  res.json({
    message: "User created",
    data: req.body
  });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

## 🔴 Running the Server

### Using Node:

```bash
node index.js
```

---

### Using Nodemon (Recommended):

```bash
npx nodemon index.js
```

---

## 🔴 Why Nodemon?

* Automatically restarts server
* Improves development workflow

---

# 🟥 5. API TESTING

---

## 🔴 Primary Tool

* Postman

---

## 🔴 What Students Must Practice

* Send GET request
* Send POST request
* Read response
* Understand status codes

---

## 🔴 Test Your Own API

```text
GET http://localhost:3000/users
POST http://localhost:3000/users
```

---

## 🔴 Alternative Tools

* Swagger (API documentation + testing)
* Apidog (modern API platform)

---

## 🔴 Important Note

> In this course, we focus on testing our own APIs, not external APIs.

---

# 🟥 6. MINI PRACTICE TASK

---

## 🔴 Task Requirements

Students must:

* Create Express server
* Implement routes:

  * `GET /users`
  * `POST /users`
* Test using Postman

---

## 🔴 Expected Outcome

Students can:

* Run server
* Send requests
* Understand responses

---

# 🟥 7. PROJECT INTRODUCTION

---

## 🔴 Project Goal

Build a real-world full-stack application.

---

## 🔴 Example Project Types

* SaaS dashboard
* Management system
* E-commerce platform

---

## 🔴 Core Features

* Authentication
* CRUD operations
* API integration
* Deployment

---

# 🟥 8. PROJECT STRUCTURE SETUP

---

## 🔴 Folder Structure

```text
project-root/

├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   ├── routes/
│   │   ├── models/
│   │   ├── services/
│   │   └── app.js
│   │
│   └── package.json
│
├── frontend/

└── README.md
```

---

## 🔴 Backend Entry File

```js
// src/app.js
const express = require('express');
const app = express();

app.use(express.json());

module.exports = app;
```

---

## 🔴 Server File

```js
// server.js
const app = require('./src/app');

app.listen(3000, () => {
  console.log('Server running...');
});
```

---

# 🟥 9. WEEK 1 CHECKPOINTS

---

By the end of Week 1, students must be able to:

* Explain how web applications work
* Understand frontend, backend, and database roles
* Understand HTTP and APIs
* Set up development environment
* Build a simple Express API
* Run server using Node and Nodemon
* Test APIs using Postman
* Understand project structure

---

# 🟥 10. KEY TAKEAWAYS

---

* Web development is about building systems
* APIs connect frontend and backend
* Tools improve productivity
* Understanding flow is critical

---

# 🚀 NEXT WEEK

👉 Backend Deep Dive:

* Controllers
* Services
* Authentication
* Database integration

---

# ✅ END OF WEEK 1
