# 📘 WEEK 3: ADVANCED BACKEND PATTERNS (AUTH, PAGINATION & API VERSIONING)
---

## 🎯 Week Objective

By the end of this week, students will:

*   **Secure the API:** Build a robust Authentication pipeline using JWT and `protect` middleware.
*   **Implement Advanced Models:** Use `mongoose-paginate-v2` for professional pagination and search.
*   **DRY (Don't Repeat Yourself):** Learn to use **Generic Functions** to handle repetitive CRUD tasks.
*   **Automate Setup:** Create powerful **Seeding Scripts** to initialize project data effortlessly.
*   **API Versioning:** Understand how to version APIs for backward compatibility.

---

# 🟥 1. SECURITY & IDENTITY: AUTHENTICATION

Identity management is the core of any secure application. We use JSON Web Tokens (JWT) to authenticate users.

### 👤 The Enhanced User Model (`src/modules/users/model.js`)

```javascript
import mongoose from "mongoose";
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";
import MongoosePaginate from "mongoose-paginate-v2";

const userSchema = new mongoose.Schema(
  {
    name: { type: String, default: null },
    username: { type: String, required: true, trim: true, unique: true },
    password: { type: String, required: true, trim: true },
    role: { type: mongoose.Schema.Types.ObjectId, ref: "Role", required: true },
    status: { type: String, enum: ["Active", "Inactive"], default: "Active" },
    branch: { type: mongoose.Schema.Types.ObjectId, ref: "Branch", default: null },
  },
  { timestamps: true, versionKey: false }
);

// Password Hashing Middleware
userSchema.pre("save", async function () {
  if (!this.isModified("password")) return;
  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
});

// Instance Method: Check Password
userSchema.methods.checkPassword = async function (password) {
  return await bcrypt.compare(password, this.password);
};

// Instance Method: Generate Token
userSchema.methods.generateToken = function () {
  return jwt.sign({ id: this._id }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRE,
  });
};

userSchema.plugin(MongoosePaginate);
export default mongoose.model("User", userSchema);
```

### 🛡️ Authentication Middleware (`src/middleware/auth.js`)

```javascript
import jwt from "jsonwebtoken";
import User from "../modules/users/model.js";

export const protect = async (req, res, next) => {
  try {
    const token = req.headers?.authorization?.split(" ")[1];
    if (!token) return res.status(401).send({ status: false, message: "Not authorized" });

    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const user = await User.findById(decoded.id).select("-password").populate("role");

    if (!user) return res.status(401).send({ status: false, message: "User not found" });

    req.user = user;
    next();
  } catch (err) {
    res.status(500).json({ status: false, message: err.message });
  }
};
```

---

# 🟥 2. ADVANCED MODELS: PAGINATION & SEARCH

Professional APIs handle large datasets using pagination and search filters.

### 📚 The Paginated Model (`src/modules/books/model.js`)

```javascript
import mongoose from "mongoose";
import MongoosePaginate from "mongoose-paginate-v2";

const bookSchema = new mongoose.Schema(
  {
    name: { type: String, required: true, trim: true },
    description: { type: String, trim: true, default: null },
    type: { type: String, required: true, trim: true },
    status: { type: String, enum: ["Active", "Inactive"], default: "Active" },
  },
  { timestamps: true, versionKey: false }
);

bookSchema.plugin(MongoosePaginate);
export default mongoose.model("Book", bookSchema);
```

---

# 🟥 3. EFFICIENCY: DRY & GENERIC LOGIC

The **DRY (Don't Repeat Yourself)** principle suggests that we should avoid duplicating similar logic across controllers.

### ⚙️ Generic Data Utils (`src/utils/generic.js`)

```javascript
export const fetchData = (Model) => async (req, res) => {
  try {
    const { options, query = {}, search = {} } = req.query;
    const { keyword, fields = [] } = search;

    let searchCriteria = {};
    if (keyword && fields.length) {
      searchCriteria = {
        $or: fields.map((field) => ({ [field]: { $regex: keyword, $options: "i" } })),
      };
    }

    const combinedQuery = { ...query, ...searchCriteria };
    const data = await Model.paginate(combinedQuery, options);
    return res.json({ status: true, data });
  } catch (err) {
    return res.status(500).json({ status: false, message: err.message });
  }
};
```

---

# 🟥 4. DATA PERSISTENCE: SEED DATA

Seeding allows us to populate the database with initial users and roles automatically.

### 🌱 The Seeding Script (`src/db/seed.js`)

```javascript
import User from "../modules/users/model.js";
import Role from "../modules/roles/model.js";

const seed = async () => {
  // Logic to find or create Admin Role and Admin User
  // Ensure the database is consistently initialized across environments
};
```

### ⌨️ Running the Seed Script

To make seeding easy, we add a script to our `package.json`:

```json
"scripts": {
  "seed": "node src/db/seed.js"
}
```

**To run the script, use the following command in your terminal:**
```bash
npm run seed
```

---

# 🟥 5. API VERSIONING

API versioning is crucial for maintaining and evolving your API without breaking existing client applications.

### 🔄 Implement Versioning (`src/index.js`)

Instead of just `app.use('/api', routes)`, use versioned paths:

```javascript
// Version 1
app.use('/api/v1/users', userV1Routes);

// Version 2 (New features without breaking v1)
app.use('/api/v2/users', userV2Routes);
```

---

# 🟥 6. WEEK 3 ASSIGNMENTS

### 📝 Final Implementation Challenge
*   **Comprehensive Application:** Apply all theories (Auth, Pagination, Seeding) to both the Bootcamp project and your own project.
*   **Identity & Security:** Apply **Authorization** and **Protect** middleware to prevent unauthorized access. Implement different user roles (e.g., Admin, User) with distinct permissions.
*   **Refactor & DRY:** Clean your code and apply the DRY principle using generic functions for all models in your project.
*   **Scale:** Ensure search and generic logic are applied to all models.
*   **Version:** Research and implement **API Versioning** (e.g., `/api/v1/...`) for your endpoints.

---

# 🚀 WEEK 3 CHECKPOINTS

* [ ] Authentication pipeline implemented and verified.
* [ ] Pagination and Search functional on all list endpoints.
* [ ] Codebase refactored to use Generic Functions (DRY).
* [ ] Initial data seeded successfully.
* [ ] API endpoints are versioned (e.g., `/api/v1`).

---

# ✅ END OF WEEK 3
