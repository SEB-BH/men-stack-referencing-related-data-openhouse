<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build the Listing Model</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create a schema for a resource and export it as a model.

## Building the listing model

The stater code provided the basic user authentication functionality for our application, so we can already check off three of our user stories:

> As a new user, I want to easily create a new account, so I can access personalized features and services on the platform.
> As a registered user, I want to log in to my account, so I can access my personal data and interact with the platform's features.
> As a logged-in user, I want the ability to log out of my account, to ensure its secure when I'm not using the platform.

Let's focus on the following user story:

> As a user, I want to create listings for my real estate properties, to advertise them to potential buyers or renters.

Before creating a listing, we need to create a Schema and Model for our listing. Let's create a new file in our `models` directory called `listing.js`. Inside `listings.js` let's create a new Schema for our listing:

```js
// models/listing.js

const mongoose = require('mongoose');

const listingSchema = new mongoose.Schema({});
```

> 🏆 You should always use the [`new`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) keyword when creating a new instance of a Schema. This is because we do not want to modify the Schema class itself; instead, we want to create a new instance of it. This is a common pattern in object-oriented programming.

First, we require Mongoose. Then, we create a new instance of a Schema using the Schema class. The Schema class takes in an object with key-value pairs that represent our model's fields and data types. Looking at our ERD for the listing model, we can see that we need the following fields:

- Street address - String and is required
- City - String and is required
- Price - Number, minimum of 0, and is required
- Size - Number, a minimum of 0, and is required

Translating this into JavaScript using the Mongoose library, we get the following:

```js
// models/listing.js

const mongoose = require('mongoose');

const listingSchema = new mongoose.Schema({
  streetAddress: {
    type: String,
    required: true,
  },
  city: {
    type: String,
    required: true,
  },
  price: {
    type: Number,
    required: true,
    min: 0,
  },
  size: {
    type: Number,
    required: true,
    min: 0,
  },
}, { timestamps: true })
```

> 🧠 The `Schema` class can take an optional second parameter that is also an object. This is where we can turn on options for a document, such as `timestamps`. Also, notice that we have kept camel case for our field names to stay consistent with the naming convention of JavaScript.

## Embedding data vs. referencing data

In Mongoose, there are two ways to represent relationships between data. The first is called embedding. This is where we nest one document inside another. The second is called referencing. This is where we store a reference to another document inside a document.

In our ERD, we have a one-to-many relationship between users and listings. We could embed an array of listing documents inside our user document, but this would mean pulling in all user data whenever accessing a listing.

We will instead opt to reference a user document inside a listing document. This will allow us to quickly check who owns a listing, which will give us the ability to control who can edit and delete a listing.

Let's put this into practice. First, we will need a field to represent this relationship. We will call this field `owner`.

```js
// models/listing.js

// ... other properties located above
  size: {
    type: Number,
    required: true,
    min: 0,
  },
  owner: {}
```

This field is still in Mongoose, so we must give it a type. However, we are not storing just a string or a number in this field. Instead, we are going to store a user's document object ID. There is a particular type in Mongoose for this called `mongoose.Schema.Types.ObjectId`. We will also need to tell Mongoose what model this field is referencing. We do this by adding the `ref` property to this field. In this case, we are referencing the `User` model.

```js
// models/listing.js

// ... other properties located above
  size: {
    type: Number,
    required: true,
    min: 0,
  },
  owner: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
  },
```

> Notice we have not added the `favoritedBy` field yet. This field will be added in a future section.

## Compiling and exporting the model

So far, we have only created a schema for our listing - a blueprint for what our listing will look like. To have access to methods that would allow us to create, read, update, and delete listings, we need to compile our schema into a model. We do this by calling the `mongoose.model()` method and passing in the name of the model we want to create and the schema we want to use to create it. Lastly, we need to export this model because we want to use it in other files:

```js
// models/listing.js

// ... listingSchema located above
const Listing = mongoose.model('Listing', listingSchema);

module.exports = Listing;
```

That's it! We now have a model that we can use to create, read, update, and delete listings.
