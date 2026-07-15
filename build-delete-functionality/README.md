<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build Delete Functionality</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement `delete` functionality in a MEN stack application.

## User Story

Now that we have a working show page, let's address the next user story:

> As a user who owns a listing, I want the option to delete a listing I created, in case the property is no longer available.

The delete functionality outlined in this user story will require a few security measures.

Currently, the owner section of our show page conditionally renders a message based on the listing's owner. This code will be useful for the delete button, as it should only be shown to the listing's owner.

In addition to conditionally rendering the delete button, we'll add an extra layer of security in our controller action. This **protected controller action** will prevent unauthorized users from deleting listings. Should a user attempt to delete a listing they don't own, they will receive a message: "You don't have permission to do that."

> 📚 The term **protected controller action** refers a controller action that is secured through an authentication or authorization check. This means that only users who meet certain criteria can access the functionality of that action.

## Conceptualizing the route

Our route should listen for `DELETE` requests on `/listings/:listingId`.

| Action | What It Does            | HTTP Verb | Route                  |
| ------ | ----------------------- | --------- | ---------------------- |
| Delete | Delete a single listing | `DELETE`  | `/listings/:listingId` |

## Building the UI

To add the UI that issues this request, we'll make a small change in `views/listings/show.ejs`.

In our view, we already have some conditional rendering based on ownership. This will be useful, as the option for deleting a listing should only appear for the listing's owner. We'll update this code with a `<button>` and a `<form>`. The `<form>` tag will be used to override the default `POST` request with a `DELETE`.

In `views/listings/show.ejs`, update the `if` block with the following:

```html
<!-- views/listings/show.ejs -->

<% if (listing.owner._id.equals(user._id)) { %>
<p>You own this!</p>

<form action="/listings/<%= listing._id %>?_method=DELETE" method="POST">
  <button type="submit">Delete this listing</button>
</form>
```

> 🚨 Make sure to include `listing._id` in the request. We'll need this to identify the listing that needs to be removed in our controller action.

## Scaffolding the function

Next, let's build the scaffolding for our controller function.

This step will help us verify the following:

1. The `listingId` is correctly passed through `req.params`.
2. The `user` object is accessible via `req.session`.

Add the following to `controllers/listings.js`:

```js
// controllers/listings.js

const deleteListing = async (req, res) => {
    console.log('listingId: ', req.params.listingId)
    console.log('user: ', req.session.user)
    res.send(`A DELETE request was issued for ${req.params.listingId}`)
}
```

## Adding the route

```js
// server.js

app.delete('/listings/:listingId', listingsCtrl.deleteListing )
```

In your browser, click on the delete button and check your terminal for the logged data.

## Writing the controller action

Now that we have confirmed access to `req.params.listingId` and `req.session.user._id`, let's complete the controller action.

The controller action should accomplish the following:

1. Retrieve a specific listing using its `ObjectId`.
2. Verify permissions by comparing the `owner` property of the listing with `req.session.user._id`. If the user making the request is not the owner, they should receive the message: "You don't have permission to do that."
3. If the user is the owner, the listing should be removed from the database using the `deleteOne()` method.

### Retrieving the listing

First, let's retrieve the listing using the `findById()` method:

```javascript
// controllers/listings.js

const deleteListing = async (req, res) => {
    const listing = await Listing.findById(req.params.listingId)
    res.send(`A DELETE request was issued for ${req.params.listingId}`)

}
```

### Implementing a permission check

Next, let's address the permission check.

Update the function with the following `if...else`:

```javascript
// controllers/listings.js

const deleteListing =  async (req, res) => {
    const listing = await Listing.findById(req.params.listingId)

    if (listing.owner.equals(req.session.user._id)) {
      console.log('Permission granted')
    } else {
      console.log('Permission denied')
    }

    res.send(`A DELETE request was issued for ${req.params.listingId}`)
}
```

### Deleting the listing

Let's take a moment to explore the [`deleteOne()`](https://mongoosejs.com/docs/5.x/docs/api/model.html#model_Model.deleteOne) method.

When called on a document instance, `deleteOne()` removes that document from the database:

```javascript
const docInstance = await Model.findById(req.params.modelId)
docInstance.deleteOne()
```

> 🧠 The `deleteOne()` method can also be called on the model. In this context, it requires an object specifying the criteria to identify the document for deletion. The first document that matches these conditions is removed from the database. We're not using this approach because we need to verify user permission before deletion.

Update the function as shown below:

```javascript
// controllers/listings.js

const deleteListing =  async (req, res) => {
    const listing = await Listing.findById(req.params.listingId)
    if (listing.owner.equals(req.session.user._id)) {
      await listing.deleteOne()
      res.redirect('/listings')
    } else {
      res.send("You don't have permission to do that.")
    }
}
```

Open up your browser and try deleting a listing that you own. If you attempt to delete a listing that you are not the owner of, you should notice that you are prevented from doing so on both the client and the server.
