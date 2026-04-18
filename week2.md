# 📘 WEEK 2: MASTERING THE BACKEND (MODULAR & RBAC)
---

## 🎯 Week Objective

By the end of this week, students will:

*   **Understand Architecture Choice:** Why Modular (Domain-based) wins for scale.
*   **Establish Solid Foundations:** Master ES Modules, Environment variables, and Git best practices.
*   **Professional DB Integration:** Connect to MongoDB using professional patterns.
*   **Implement Feature Modules:** Build robust Models, Controllers, and Routes using Joi and Bcrypt.
*   **Master Security Pipeline:** Implement custom Auth & RBAC (Admin/Entry) logic.
*   **Global Error Handling:** Manage 404s and test everything in Postman.

---

# 🟥 1. ARCHITECTURE THEORY: MVC VS. MODULAR

Before writing code, we must decide how to organize it. In this bootcamp, we choose the **Modular (Vertical)** approach.

| Feature | **MVC (Layered/Horizontal)** | **Modular (Domain-based/Vertical)** |
| :--- | :--- | :--- |
| **Organization** | By type (Models, Views, Controllers). | By feature (Auth, Books, Users). |
| **Scalability** | Harder as folders get massive. | Easier; just add a feature folder. |
| **Complexity** | Simple for beginners. | Industry standard for production. |
| **Isolation** | Code is scattered. | Complete feature isolation. |

---

# 🟥 2. PROJECT FOUNDATIONS (ESM & CONFIG)

### ⚙️ ES Modules (ESM) Transition
Modern Node.js uses `import/export`. We enable this in `package.json`:
```json
{ "type": "module" }
```

### 📂 The Modular Folder Structure
```text
/src
  /config                   # Global configs (db.js)
  /features (or modules)    # Domain-based features
    /auth                   # Auth logic
    /users                  # User management
    /locations              # Location management
  /middlewares              # Auth & Error middlewares
  /utils                    # Helpers (catchAsync, AppError)
  index.js                  # App & Server entry point
```

### 🔑 Environment & Git (`.env` & `.gitignore`)
We use `dotenv` to protect our secrets.

```javascript
// index.js
import "dotenv/config";
const PORT = process.env.PORT || 5000;
```

**Essential `.gitignore`:**
```text
node_modules
.env
```

---

# 🟥 3. DATABASE INTEGRATION

Connecting to the database is the first step in our implementation phase.

### 🔌 MongoDB Connection (`src/config/db.js`)
```javascript
import mongoose from "mongoose";

export const connectMongoDb = (url) => {
  mongoose.connect(url)
    .then(() => console.log("✅ MongoDB connected"))
    .catch((err) => console.log("❌ DB Error:", err));
};
```

---

# 🟥 4. FEATURE IMPLEMENTATION: DOMAIN MODULES

Each feature module contains its own **Model**, **Controller**, and **Routes**.

### 🔴 The Modular Model (`features/users/model.js`)
Includes **Joi Validation**, **HashingHooks**, and **Methods**.

```javascript
import mongoose from "mongoose";
import Joi from "joi";
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";

const userSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  role: { type: String, enum: ["Admin", "Entry"], required: true }
}, { timestamps: true });

// Joi Validation
export const validate = (user) => {
  return Joi.object({
    username: Joi.string().min(2).required(),
    password: Joi.string().required(),
    role: Joi.string().valid("Admin", "Entry").required()
  }).validate(user);
};

// Password Hashing
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

export default mongoose.model("User", userSchema);
```

### 🔴 The Smart Controller (`features/users/controller.js`)
```javascript
import User, { validate } from "./model.js";

export const createUser = async (req, res) => {
  try {
    const { error } = validate(req.body);
    if (error) return res.status(400).send({ status: false, message: error.details[0].message });
    // ... logic for creation
  } catch (err) {
    res.status(500).json({ status: false, message: err.message });
  }
};
```

---

# 🟥 5. SECURITY PIPELINE & RBAC

Identity and access control are the final pieces of backend logic.

### 🛡️ Auth Logic (`features/auth/controller.js`)
*   **Login:** Find user → compare password → generate JWT.
*   **Token:** Sent to client for all future requests.

### 🔑 Simplified RBAC (`middlewares/auth.js`)
In our project, we use binary roles:
*   **Admin:** Full access.
*   **Entry:** Library management only.

```javascript
export const authorize = (roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ status: false, message: "Forbidden" });
    }
    next();
  };
};
```

---

# 🟥 6. ERROR HANDLING & VERIFICATION

### 🔴 Incorrect Route Handling (404)
```javascript
app.use((req, res) => {
  res.status(404).json({
    status: "failed",
    message: `Route not found: ${req.originalUrl}`
  });
});
```

### 🧪 Postman Verification
- Run your server.
- Authenticate and get your JWT.
- Test Admin routes with an Entry token to verify **RBAC security**.

---

# 🟥 7. WEEK 2 ASSIGNMENTS

### 📝 Assignment 1: The "Smart Kutub Finder" Re-build
**Goal:** Master the backend functionalities we covered during this session.
*   **Task:** Build the full backend for the "Smart Kutub Finder" from scratch (from your mind!).
*   **Rules:** Do not copy-paste. If you get stuck, refer to your notes or the GitHub repository, but try to understand the logic before writing it yourself.

### 📝 Assignment 2: Your Own Modular Project
**Goal:** Apply all concepts to a fresh project and explore advanced authorization.
*   **Core Requirements:**
    -   Create User and Role management.
    -   Implement full Authentication and Authorization.
*   **Advanced Authorization (ABAC):**
    -   Use **Attribute-Based Access Control** for your project.
    -   **Requirement:** Use a specialized package like **CASL** (Recommended).
    -   **Alternatives for Research:** `accesscontrol`, `casbin`, `rbac`, or `acl`.
*   **Quality Check:** Apply every single idea we covered in the session (Joi, Bcrypt, JWT, Modular structure, 404 handler, etc.).
*   **The "Plus Three" Rule:** Identify and implement **at least 3 extra concepts** that we did not cover in class (e.g., File Uploads, Email Notification, Rate Limiting, API Documentation like Swagger, etc.).

---

# 🚀 WEEK 2 CHECKPOINTS

* [ ] Architecture choice (Modular) understood.
* [ ] `.env` and `.gitignore` correctly configured.
* [ ] MongoDB connected successfully.
* [ ] **Modular logic** implemented (Models, Controllers, Routes).
* [ ] **RBAC pipeline** (Admin vs. Entry) verified.
* [ ] 404 handler catching incorrect routes.
* [ ] Full feature set verified in Postman.

---

# ✅ END OF WEEK 2
