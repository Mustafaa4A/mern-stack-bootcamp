# 📘 WEEK 2: MASTERING THE BACKEND (MODULAR & RBAC)
---

## 🎯 Week Objective

By the end of this week, students will:

*   **Implement Professional Modular Architecture:** Using `index.js` as the entry point and domain-based features.
*   **Master ES Modules (ESM):** Using `import`/`export` and `"type": "module"`.
*   **Build with Industry Libraries:** Using `Joi`, `Bcrypt`, `JWT`, and `Mongoose-Paginate-v2`.
*   **Implement Simplified RBAC:** Logic for `Admin` vs. `Entry` roles.
*   **Full Feature Development:** Creating complete Models, Controllers, and Routes for "Smart Kutub Finder".

---

# 🟥 1. ARCHITECTURE & PROJECT SETUP

### 📂 The Modular Folder Structure
We group everything by its business domain (feature).

```text
/src
  /config                   # Global configs (db.js)
  /features (or modules)    # THE HEART OF MODULAR DESIGN
    /auth                   # Auth Routes, Controller
    /users                  # User Routes, Controller, Model
    /locations              # Location Routes, Controller, Model
  /middlewares              # Global Protect & Authorize Middlewares
  /utils                    # AppError, catchAsync, fetchData logic
  index.js                  # Express setup and Server entry point
```

### ⚙️ ESM Initialization
Update `package.json` to enable modern JavaScript.
```json
{
  "type": "module",
  "dependencies": {
    "express": "^4.21.0",
    "mongoose": "^8.0.0",
    "joi": "^17.10.0",
    "bcrypt": "^5.1.0",
    "jsonwebtoken": "^9.0.0",
    "mongoose-paginate-v2": "^1.7.0",
    "joi-objectid": "^4.0.0"
  }
}
```

---

# 🟥 2. THE MODULAR MODEL (SCHEMA & VALIDATION)

Every model must include **Validation** (Joi) and **Hooks** (Bcrypt).

### 🔴 Sample: User Model (`features/users/model.js`)
```javascript
import mongoose from "mongoose";
import Joi from "joi";
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";
import MongoosePaginate from "mongoose-paginate-v2";
import JoiObjectId from "joi-objectid";

const ObjectId = JoiObjectId(Joi);

const userSchema = new mongoose.Schema({
  name: { type: String, default: null },
  username: { type: String, required: true, trim: true, unique: [true, "User is already taken"] },
  password: { type: String, required: true, trim: true },
  role: { type: String, enum: ["Admin", "Entry"], required: true },
  status: { type: String, enum: ["Active", "Inactive"], default: "Active" },
  createdBy: { type: mongoose.Schema.Types.ObjectId, ref: "User", default: null }
}, { timestamps: true, versionKey: false });

// 🛡️ Data Validation (Joi)
export const validate = (user) => {
  const Schema = Joi.object({
    name: Joi.string(),
    username: Joi.string().min(2).max(20).required(),
    password: Joi.string().required(),
    role: Joi.string().valid("Admin", "Entry").required(),
    status: Joi.string()
  }).unknown(false).strict();
  return Schema.validate(user);
};

// 🔒 Hash password before save
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();
  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

// ✅ Method to check password
userSchema.methods.checkPassword = async function (password) {
  return await bcrypt.compare(password, this.password);
};

// 🎫 Method to generate token
userSchema.methods.generateToken = async function () {
  return jwt.sign({ id: this._id }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRE,
  });
};

userSchema.plugin(MongoosePaginate);
export default mongoose.model("User", userSchema);
```

---

# 🟥 3. THE SMART CONTROLLER (CRUD & FETCH)

We focus on clean, asynchronous logic using the user-provided patterns.

### 🔴 Sample: User Controller (`features/users/controller.js`)
```javascript
import User, { validate } from "./model.js";

export const createUser = async (req, res) => {
  try {
    const { error } = validate(req.body);
    if (error) return res.status(400).send({ status: false, message: error.details[0].message });

    const exists = await User.findOne({ username: req.body.username });
    if (exists) return res.status(400).send({ status: false, message: "User already exists" });

    const user = await User.create({ ...req.body, createdBy: req?.user?._id });
    res.status(201).send({ status: true, message: "User created successfully", data: user });
  } catch (err) {
    res.status(500).json({ status: false, message: err.message });
  }
};

export const deleteUser = async (req, res) => {
  try {
     const { id } = req.params;
     const deletedUser = await User.findOneAndDelete({ _id: id });
     if (!deletedUser) return res.status(400).json({ status: false, message: "Invalid action" });
     res.send({ status: true, message: "User deleted successfully", data: deletedUser });
  } catch (err) {
     res.status(500).json({ status: false, message: err.message });
  }
};
```

---

# 🟥 4. AUTHENTICATION & TOKEN LOGIC

### 🔴 Auth Controller (`features/auth/controller.js`)
```javascript
import User from "../users/model.js";

export const login = async (req, res) => {
  try {
    const { username, password } = req.body;
    const user = await User.findOne({ username });

    if (!user || !(await user.checkPassword(password))) {
      return res.status(400).json({ status: false, message: "Invalid credentials" });
    }

    if (user.status !== "Active") {
      return res.status(403).json({ status: false, message: "Account inactive, contact admin" });
    }

    const token = await user.generateToken();
    const { password: pass, ...data } = user.toObject();

    res.status(200).json({ status: true, message: "Logged in", data: { ...data, token } });
  } catch (err) {
    res.status(500).json({ status: false, message: err.message });
  }
};
```

---

# 🟥 5. SIMPLIFIED RBAC (ADMIN vs. ENTRY)

For the **Smart Kutub Finder**, we implement binary logic:

*   **Admin:** Full access to everything.
*   **Entry:** Full access to "Library" operations, but NO access to "User" management.

### 🔴 Auth Middleware (`middlewares/auth.js`)
```javascript
export const protect = async (req, res, next) => {
  // 1. Get token from header
  // 2. Verify token
  // 3. Attach User to req.user
};

export const authorize = (roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(401).send({
        status: false,
        message: `Forbidden: Access restricted to ${roles.join(" or ")}`
      });
    }
    next();
  };
};

// 🗺️ Application Map:
// User CRUD: router.post('/create', protect, authorize(['Admin']), createUser);
// Book CRUD: router.post('/create', protect, authorize(['Admin', 'Entry']), createBook);
```

---

# 🟥 6. API TESTING WITH POSTMAN

1.  **Login Request:** Capture the `token`.
2.  **Authorization Header:** Use `Bearer <token>` in the Headers tab.
3.  **Tests:** Try to access User creation with an 'Entry' token and verify you get 401/403.

---

# 🚀 WEEK 2 CHECKPOINTS

* [ ] `index.js` entry point created.
* [ ] Folder structure set to **Modular/Domain-based**.
* [ ] **Joi validation** implemented in every feature model.
* [ ] **Auth Pipeline:** Bcrypt hashing → JWT generation complete.
* [ ] **Simplified RBAC:** Entry vs Admin role restrictions verified.
* [ ] Full CRUD for **Smart Kutub Finder** entities functional.
* [ ] All modular endpoints tested in Postman with proper roles.

---

# ✅ END OF WEEK 2
