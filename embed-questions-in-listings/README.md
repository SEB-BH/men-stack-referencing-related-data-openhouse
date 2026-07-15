<h1>
  <span class="headline">OpenHouse</span>
  <span class="subhead">Embed Questions in Listings</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to embed questions inside a listing and allow users to create and view those questions.

## User story

Users may want more information before arranging a property viewing.

> As an interested user, I want to ask a question about a listing so that I can learn more about the property.

For example, a user might ask:

* Is parking included?
* Are utilities included in the rent?
* When is the property available?
* Does the building have an elevator?

We will add a question form to each listing's show page.

## Planning the relationship

A question belongs to one specific listing:

```text
Listing
└── Questions
    ├── Question 1
    ├── Question 2
    └── Question 3
```

A question does not need its own page or collection. We will normally read it when we read its listing.

For these reasons, we will **embed** questions inside the listing document.

Each question will contain:

* The question's text
* A reference to the user who asked the question
* Timestamps showing when it was created and updated

Our data will have a structure similar to this:

```js
{
  streetAddress: 'Building 12, Road 2407',
  city: 'Manama',
  price: 450,
  questions: [
    {
      text: 'Is parking included?',
      author: ObjectId('...'),
      createdAt: Date,
      updatedAt: Date
    }
  ]
}
```

> 💡 The question itself is embedded inside the listing. However, its `author` field references a document from the `User` collection. An application can use embedding and referencing together.

## Plan the route

The question form will appear directly on the listing show page. We do not need a separate new page for questions.

| Action            | HTTP method | Route                            |
| ----------------- | ----------- | -------------------------------- |
| Create a question | `POST`      | `/listings/:listingId/questions` |

The `listingId` tells the server which listing should receive the new question.

## Add the question schema

Open `models/listing.js`.

Define a `questionSchema` above the existing `listingSchema`:

```js
// models/listing.js

const mongoose = require('mongoose')

const questionSchema = new mongoose.Schema(
  {
    text: {
      type: String,
      required: true,
      trim: true,
    },
    author: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
      required: true,
    },
  },
  {
    timestamps: true,
  }
)

const listingSchema = new mongoose.Schema({
  // Existing listing fields
})
```

The `questionSchema` describes one question.

The `timestamps` option automatically adds two fields:

* `createdAt`
* `updatedAt`

Next, add a `questions` field to the existing `listingSchema`:

```js
// models/listing.js

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
  owner: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true,
  },
  favoritedByUsers: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
    },
  ],
  questions: [questionSchema],
})
```

The following line tells Mongoose that each listing can contain an array of question documents:

```js
questions: [questionSchema]
```

New listings will begin with an empty `questions` array.

> 🧠 Mongoose automatically gives each embedded question its own `_id`. This will be useful if we later add edit or delete functionality.

## Create the questions controller

Create a new controller file:

```bash
touch controllers/questions.js
```

Open `controllers/questions.js` and import the `Listing` model:

```js
// controllers/questions.js

const Listing = require('../models/listing.js')
```

Next, create a function that adds a question to a listing:

```js
// controllers/questions.js

const Listing = require('../models/listing.js')

async function create(req, res) {
  try {
    const listing = await Listing.findById(req.params.listingId)

    if (!listing) {
      return res.redirect('/listings')
    }

    const questionData = {
      text: req.body.text,
      author: req.session.user._id,
    }

    listing.questions.push(questionData)

    await listing.save()

    res.redirect(`/listings/${listing._id}`)
  } catch (error) {
    console.log(error)
    res.redirect(`/listings/${req.params.listingId}`)
  }
}

module.exports = {
  create: create,
}
```

Let's break down the important steps.

First, we find the parent listing:

```js
const listing = await Listing.findById(req.params.listingId)
```

Next, we prepare the question data:

```js
const questionData = {
  text: req.body.text,
  author: req.session.user._id,
}
```

The question text comes from the form. The author comes from the logged-in user's session.

We then add the question to the embedded array:

```js
listing.questions.push(questionData)
```

At this point, the listing has changed in our application, but the change has not been saved to MongoDB.

We must save the parent listing:

```js
await listing.save()
```

> 💡 When working with an embedded document, we update the parent document and then save the parent document.

## Connect the route

Open `server.js`.

Import the questions controller with the other controller files:

```js
// server.js

const questionsCtrl = require('./controllers/questions.js')
```

Add the route:

```js
// server.js

app.post(
  '/listings/:listingId/questions',
  isSignedIn,
  questionsCtrl.create
)
```

The route passes the request to the `create` function inside `controllers/questions.js`.

The `isSignedIn` middleware prevents logged-out users from creating questions.

## Add the question form

Open `views/listings/show.ejs`.

Add the following section beneath the listing's details:

```html
<!-- views/listings/show.ejs -->

<section>
  <h2>Questions about this property</h2>

  <form
    action="/listings/<%= listing._id %>/questions"
    method="POST"
  >
    <label for="question-text">
      Ask a question:
    </label>

    <textarea
      id="question-text"
      name="text"
      rows="4"
      required
    ></textarea>

    <button type="submit">Ask Question</button>
  </form>
</section>
```

The form sends a `POST` request to the nested question route:

```text
/listings/:listingId/questions
```

The `name` of the `<textarea>` matches the `text` field in our question schema:

```html
name="text"
```

This makes the question available as:

```js
req.body.text
```

## Populate the question authors

Each question's `author` field currently contains a user's `ObjectId`.

To display the author's username, we must populate the referenced user data.

Open `controllers/listings.js` and locate the existing `show` function.

Update the Mongoose query so that it populates both the listing owner and the question authors:

```js
// controllers/listings.js

async function show(req, res) {
  try {
    const listing = await Listing.findById(
      req.params.listingId
    )
      .populate('owner')
      .populate('questions.author')

    if (!listing) {
      return res.redirect('/listings')
    }

    const userHasFavorited = listing.favoritedByUsers.some(
      function (userId) {
        return userId.equals(req.session.user._id)
      }
    )

    res.render('listings/show.ejs', {
      listing: listing,
      userHasFavorited: userHasFavorited,
    })
  } catch (error) {
    console.log(error)
    res.redirect('/listings')
  }
}
```

Your existing function may contain slightly different code. Keep the existing functionality and add:

```js
.populate('questions.author')
```

The dot notation describes the path to populate:

```text
questions → author
```

After populating this path, each `question.author` contains the user's data instead of only an `ObjectId`.

## Display the questions

Return to `views/listings/show.ejs`.

Under the question form, check whether the listing has any questions:

```html
<!-- views/listings/show.ejs -->

<h3>Questions from users</h3>

<% if (listing.questions.length === 0) { %>
  <p>No questions have been asked yet.</p>
<% } else { %>
  <% listing.questions.forEach(function (question) { %>
    <article>
      <p><%= question.text %></p>

      <p>
        Asked by <%= question.author.username %>
      </p>
    </article>
  <% }) %>
<% } %>
```

The complete questions section should now look similar to this:

```html
<!-- views/listings/show.ejs -->

<section>
  <h2>Questions about this property</h2>

  <form
    action="/listings/<%= listing._id %>/questions"
    method="POST"
  >
    <label for="question-text">
      Ask a question:
    </label>

    <textarea
      id="question-text"
      name="text"
      rows="4"
      required
    ></textarea>

    <button type="submit">Ask Question</button>
  </form>

  <h3>Questions from users</h3>

  <% if (listing.questions.length === 0) { %>
    <p>No questions have been asked yet.</p>
  <% } else { %>
    <% listing.questions.forEach(function (question) { %>
      <article>
        <p><%= question.text %></p>

        <p>
          Asked by <%= question.author.username %>
        </p>
      </article>
    <% }) %>
  <% } %>
</section>
```

## Test the feature

Start the application:

```bash
npm run dev
```

Then complete the following steps:

1. Sign in.
2. Visit a listing's show page.
3. Enter a question such as “Is parking included?”
4. Submit the form.
5. Confirm that you return to the same listing.
6. Confirm that the question and username appear on the page.
7. Open MongoDB Atlas and inspect the listing document.

Inside the listing document, you should see a `questions` array:

```js
questions: [
  {
    text: 'Is parking included?',
    author: ObjectId('...'),
    _id: ObjectId('...'),
    createdAt: Date,
    updatedAt: Date
  }
]
```

## What happened?

When the form was submitted:

1. The browser sent the question text to the server.
2. The route passed the request to `questionsCtrl.create`.
3. The controller found the listing.
4. The controller pushed a new question into `listing.questions`.
5. The controller saved the listing.
6. The browser was redirected back to the listing's show page.
7. The show controller populated each question's author.
8. EJS displayed the questions.

The question is stored inside the listing:

```js
listing.questions
```

The user is still stored separately in the `users` collection:

```js
question.author
```

This is an example of using **embedding and referencing together**.

## 🎓 You Do

Add questions to at least two different listings.

For each question, confirm that:

* The question appears only on the correct listing.
* The question contains its own `_id`.
* The question's author is stored as an `ObjectId`.
* The author's username appears after the `author` path is populated.

Then answer this question:

> Why does it make sense to embed a question inside a listing instead of creating a separate questions collection?
