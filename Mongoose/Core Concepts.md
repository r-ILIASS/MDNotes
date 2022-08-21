# Core Concepts

- **Schema:** Defines what the structure of your data looks like.
- **Model:** Models are fancy constructors compiled from Schema definitions. An instance of a model is called a document. Models are responsible for creating and reading documents from the underlying MongoDB database.
- **Query:** A query we are making against the mongodb database.

## Custom Validation On Schemas

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: String,
  age: {
    type: Number,
    min: 1,
    max: 100,
    validate: {
      validator: (value) => value % 2 === 0,
      message: (props) => `${props.value} is not an even number`, // fires when the validator fails
    },
  },
});

const User = mongoose.model("User", userSchema);

export { User };
```

> **Built-in or custom validation only run Model.prototype.save() and Model.prototype.creat(), so it's always recommended to use Model.save() or model.create() and avoid Model.findOneAndUpdate() for example as they skip mongoose validation**

## Schema Methods/Virtuals

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: String,
  age: {
    type: Number,
    min: 1,
    max: 100,
    validate: {
      validator: (value) => value % 2 === 0,
      message: (props) => `${props.value} is not an even number`, // fires when the validator fails
    },
  },
});

userSchema.methods.sayHi = function () {
  console.log(`Hi, my name is ${this.name}`);
}; // can be called later on a user: user.sayHi()

userSchema.statics.findByName = function (name) {
  return this.find({name: new RegExp(name, "i")}).
}; // can be called later on a model instance: User.findByName("iliass")

userSchema.query.byName = function (name) {
  return this.where({name: new RegExp(name, "i")}).
}; // can be called later on a query: User.find().byName("iliass")

userSchema.virtual("namedEmail").get(function() {
  return `${this.name} <${this.email}>`
})

const User = mongoose.model("User", userSchema);

export { User };
```

## Referencing Another Model

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: String,
  role: {
    type: mongoose.SchemaTypes.ObjectId,
    ref: "Role", // This is a reference the Roles model
  },
});

const User = mongoose.model("User", userSchema);

export { User };
```

## Usefull Model Methods

```js
import { User } from "../models/user.js";

await User.deleteOne({ name: "iliass" }); // deletes the first document that matches the query
```

### Custom Querying

```js
import { User } from "../models/user.js";

// gets documents where the age is between 12 and 30 and the name is iliass, then selects only the age filed and return it
await User.where("age")
  .gt(12)
  .lt(30)
  .where("name")
  .equals("iliass")
  .select("age");
```

### Mongoose Middleware

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: String,
  age: {
    type: Number,
    min: 1,
    max: 100,
    validate: {
      validator: (value) => value % 2 === 0,
      message: (props) => `${props.value} is not an even number`, // fires when the validator fails
    },
  },
});

// mongoose middleware
userSchema.pre("save", function(next) {
  this.updatedAt = Date.now();
  next();
})

userSchema.post("save", function(doc, next) {
  doc.sayHi(); // this is the document that has been saved
  next();
})

const User = mongoose.model("User", userSchema);

export { User };
`
```
