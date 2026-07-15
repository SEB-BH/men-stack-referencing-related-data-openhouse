<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Add Favorites Functionality to the Show Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement functionality for favoriting a resource.

## User story

With our `listingSchema` updated, we should be ready to handle the following user stories:

> As a user, I want to see the number of favorites a listing has received, to gauge its popularity and potential demand.
> As a user, I want to favorite a listing, so I can easily find and review it later.

## Conceptualizing the route

Our route should listen for `POST` requests on `/listings/:listingId/favorited-by/:userId`.

| Action | What It Does        | HTTP Verb | Route                                       |
| ------ | ------------------- | --------- | ------------------------------------------- |
| Create | Favorites a listing | `POST`    | `/listings/:listingId/favorited-by/:userId` |

> 🧠 We could technically omit `:userId` from our route since our auth middleware already gives us access to `user` through `req.session`. However, the pattern you see here is common in development and worth practicing.

## Building the UI

Next let's add a 'Favorites' section to `views/listings/show.ejs`.

In this section, we'll display the number of people that have favorited a listing, and a button for marking the listing as a favorite.

We'll need to include two pieces of data in our `POST` request:

1. `listing._id`
2. `user._id`

Add the following to `views/listings/show.ejs`:

```html
<!-- views/listings/show.ejs -->

<h2>Favorites</h2>
<p>Favorited by <%= listing.favoritedByUsers.length %> people.</p>
<form
  action="/listings/<%= listing._id %>/favorited-by/<%= user._id %>"
  method="POST"
>
  <button type="submit">Favorite it!</button>
</form>
```

## Scaffolding the function

Next, let's build the scaffolding for our controller function. With this we'll confirm that both the `listingId` and the `userId` are accessible through `req.params`.

Add the following to `controllers/listings.js`:

```js
// controllers/listings.js

const favorite = async (req, res) => {
    console.log('userId: ', req.params.userId)
    console.log('listingId: ', req.params.listingId)
    res.send(`Request to favorite ${req.params.listingId}`)
}
```

## Adding the route

```js
// server.js

app.post('/listings/:listingId/favorited-by/:userId', listingsCtrl.favorite)
```

In your browser, click on the 'Favorite it!' button and check your terminal for the logged data.

## Writing the controller action

Now that we have confirmed access to `req.params.listingId` and `req.params.userId`, let's complete the controller action.

Our controller action will update a listing by adding a `userId` to the `favoritedByUsers` array. For this we'll utilize the [`findByIdAndUpdate()`](<https://mongoosejs.com/docs/api/model.html#Model.findByIdAndUpdate()>) method.

The `findByIdAndUpdate()` accepts two arguments:

1. An `ObjectId` for locating the document.
2. An object containing data to update the document with.

The update object we use here will be different from what you've seen in the past, as we need to modify an array inside the listing document. For this we'll make use of MongoDB's _`$push`_ operator.

The [_`$push`_](https://www.mongodb.com/docs/manual/reference/operator/update/push/) operator is used to add a new value to an array. The operator requires that we specify the **target array** and the **value** to add.

When used inside an update object, we would see something like the following syntax:

```js
const updateObject = { $push: { targetArray: newValue } };
```

For our purposes, we'll be adding `req.params.userId` to `favoritedByUsers`.

Update the function as shown below:

```js
// controllers/listings.js

const favorite = async (req, res) => {
    await Listing.findByIdAndUpdate(req.params.listingId, {
      $push: { favoritedByUsers: req.params.userId },
    })
    res.redirect(`/listings/${req.params.listingId}`)
}
```

At this point, you should be able to favorite a listing!

## Modifying the view

Now that we can favorite a listing, let's touch up our **show page** with some conditional rendering.

Once a user favorites a listing, they should no longer see the 'Favorite it!' button. We can determine if a user has favorited a listing by checking if their `ObjectId` is inside the `favoritedByUsers` array.

This determination can actually be handled in the corresponding controller action by creating a boolean called `userHasFavorited`. We can then pass this boolean to the show view, and conditionally render the 'Favorite it!' button based on its value.

Update the 'show' controller as demonstrated below:

```js
// controllers/listings.js

const show = async (req, res) => {
    const populatedListings = await Listing.findById(
      req.params.listingId
    ).populate('owner')

    const userHasFavorited = populatedListings.favoritedByUsers.some((user) =>
      user.equals(req.session.user._id)
    )

    res.render('listings/show.ejs', {
      listing: populatedListings,
      userHasFavorited: userHasFavorited,
    })
}
```

> 💡 The [`some()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) array method returns `true` if at least one element passes the test in the provided callback. In this example, if at least one `ObjectId` in the array matches that of the current user, the value of `userHasFavorited` will be `true`. If there is no matching `ObjectId`, `userHasFavorited` will have a value of `false`.

Next we'll update our view with an `if...else` block:

```html
<!-- views/listings/show.ejs -->

<h2>Favorites</h2>
<p>Favorited by <%= listing.favoritedByUsers.length %> people.</p>

<% if (userHasFavorited) { %>
<p>You've favorited this listing!</p>
<% } else { %>
<p>You haven't favorited this listing.</p>
<% } %>
```

> 🏆 Notice how concise our conditional rendering is here. This is a direct result of the additional work we did in our controller action. While it's possible to achieve this functionality entirely in the view, it is better practice to handle complex logic like this in controllers.

Finally, move the `<form>` and `<button>` into the `else` block:

```html
<!-- views/listings/show.ejs -->

<h2>Favorites</h2>

<p>Favorited by <%= listing.favoritedByUsers.length %> people.</p>

<% if (userHasFavorited) { %>
<p>You've favorited this listing!</p>
<% } else { %>
<p>You haven't favorited this listing.</p>

<form
  action="/listings/<%= listing._id %>/favorited-by/<%= user._id %>"
  method="POST"
>
  <button type="submit">Favorite it!</button>
</form>

<% } %>
```

In your browser, try favoriting a listing. Verify that the UI changes as expected!
