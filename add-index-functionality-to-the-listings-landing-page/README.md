<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Add Index Functionality to the Listings Landing Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to apply the `populate()` method to index functionality.

## User story

Now that we have data in our database, let's focus on the next user story:

> As a user, I want to view all the listings created by every user on a single page, to explore available properties.


## 🎓 You Do: Log all listings

We have determined that we need a route for `GET` requests to `/listings`. Let's build it! Inside of `controllers/listings.js` do the following steps in your `index` function:

1. Using the `Listing` model, find all listings.
2. `console.log()` the listings.
3. `res.send()` a message that says "Listings index page".
4. `console.log()` any errors that occur.
5. Test your route in the browser by going to `localhost:3000/listings` manually.

> 🚨 Make sure you have started your server with `nodemon` before testing your route.

After completing the above steps, you should see the message "Listings index page" in the browser. 

Make sure you have debugged your application before moving on!

At the moment, we have a route and controller defined, but we are not yet rendering the data to the view. When we display our listings, we'll want to include the `city` that each listing is in, as well as the `username` of the listing's `owner`. To accomplish this, we'll need to make use of the Mongoose `populate()` method.

## The `populate()` method

Recall the `owner` property on our `listingSchema`:

```javascript
// models/listing.js
  owner: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
  },
```

The `owner` property stores the `ObjectId` of a user document. This pattern is known as _referencing_, where a document in one collection links to a document in another collection by storing a record of its `ObjectId`.

Unlike embedding, where a parent document includes related sub-documents, with referencing we only store the `ObjectId` of the related document, not the actual data.

While we could execute a separate database operation to retrieve the relevant user document, Mongoose provides a method for joining related data with [`populate()`](https://mongoosejs.com/docs/populate.html#population).

The `populate()` method replaces a specified path in a document with documents from other collections. It accepts a `string` specifying the path in the document that needs to be populated with data.

Take a look at the example below:

```javascript
const populatedListings = await Listing.find({}).populate('owner');
```

In this example, the `populate()` method is called on a `find()` operation. It specifies that the `ObjectId` held in the `owner` field should be replaced with the corresponding user document. This will apply to all listings (assuming they have an active `owner` field) returned by the `find()` operation.

## Applying the `populate()` method

Let's apply the `populate()` method to our existing `index` controller function. We'll use it to fill the `owner` path of each `listing` with the referenced user document. Later on, this will allow us to display the name of an owner beside each listing in our view.

We'll start by getting a sense of how the `populate()` method changes the data returned from our `find()` operation.

Update your controller function as shown below:

```javascript
// controllers/listings.js

const index = async (req, res) => {
    const populatedListings = await Listing.find({}).populate('owner')
    console.log('Populated Listings:', populatedListings)
    res.render('listings/index.ejs')
}
```

In your browser, navigate to the listings landing page and check your terminal.

You should see something like the following:

```plaintext
[
  {
    _id: new ObjectId('65a19015b411a7e80f3d79a3'),
    streetAddress: '100 Random Road',
    city: 'New York',
    price: 2000,
    size: 500,
    owner: {
      _id: new ObjectId('65a18ec324705dcb3e2b64aa'),
      username: 'alex@email.com',
      password: '**************',
      __v: 0
    },
    __v: 0
  }
]
```

Before applying `.populate('owner')`, the `owner` property of each `listing` only held an `ObjectId`. Now that the related data is being joined, the `ObjectId` has been replaced with an actual document.

## Rendering data to the view

Let's render our newly populated listing data.

First, update the controller so that `populatedListings` is passed to the view as `listings`:

```javascript
// controllers/listings.js

const index = async (req, res) => {
    const populatedListings = await Listing.find({}).populate('owner')

    // Add the following:
    res.render('listings/index.ejs', {
      listings: populatedListings,
    })
}
```

And finally, add the following to the `<body>` of `views/listings/index.ejs`:

```html
<!-- views/listings/index.ejs -->

<ul>
  <% listings.forEach((listing) => { %>
  <li>
    A listing in <%= listing.city %> owned by <%= listing.owner.username %>.
  </li>
  <% }) %>
</ul>
```
