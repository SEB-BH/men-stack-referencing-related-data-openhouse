<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build the Edit Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement edit functionality in a MEN stack application.

## User Story

Let's address the next user story:

- > As a user who owns a listing, I want to edit details of my listings, to ensure the information stays up-to-date.

Much like creating a new resource, this functionality will require two requests:

1. A `GET` to render the view.
2. A `PUT` to update the resource.

We will begin with the `GET` request that renders the view.

## Conceptualizing the route

Our route should listen for `GET` requests on `/listings/:listingId`.

| Action | What It Does                                  | HTTP Verb | Route                  |
| ------ | --------------------------------------------- | --------- | ---------------------- |
| Edit   | Renders a view for editing a specific listing | `GET`     | `/listings/:listingId` |

## Building the UI

Next we'll need to add an anchor tag to our show view. Like our delete button, this link should only be visible to the owner of a listing.

Update the `if` block as shown below:

```html
<!-- views/listings/show.ejs -->

<% if (listing.owner._id.equals(user._id)) { %>
<p>You own this!</p>
<a href="/listings/<%= listing._id %>/edit">Edit this listing</a>
```

## Scaffolding the function

Next, let's build the scaffolding for our controller:

```js
// controllers/listings.js

const edit = async (req, res) => {
    console.log('listingId: ', req.params.listingId)
    res.send(`Listings edit view`)
}
```
## Adding the route

```js
// server.js

app.get('/listings/:listingId/edit', listingsCtrl.edit )
```

In your browser, navigate to the edit page and check your terminal for the logged data.

## Writing the controller action

With access to the `listingId` we can build out the rest of the function, passing the listing to our view:

```js
// controllers/listings.js

const edit = async (req, res) => {
    const currentListing = await Listing.findById(req.params.listingId)
    res.render('listings/edit.ejs', {
      listing: currentListing,
    })
}
```

## Rendering the view

Lastly, let's create our edit view.

Run the following command in your terminal to create an `edit.ejs` file:

```bash
touch views/listings/edit.ejs
```

Next, let's add the following to `views/listings/edit.ejs`:

```html
<!-- views/listings/edit.ejs -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Edit <%= listing.name %></title>
  </head>
  <body>
    <%- include('../partials/_navbar.ejs') %>
    <h1>Edit a Listing!</h1>
  </body>
</html>
```

## Building the Form

Next, after our header, we'll want to add the following `<form>`:

```html
<!-- views/listings/edit.ejs -->

<form>
  <label for="streetAddress">Street address:</label>
  <input
    type="text"
    name="streetAddress"
    id="street"
    value="<%= listing.streetAddress %>"
  />
  <label for="city">City:</label>
  <input type="text" name="city" id="city" value="<%= listing.city %>" />
  <label for="price">Price:</label>
  <input
    type="number"
    name="price"
    id="price"
    value="<%= listing.price %>"
    min="0"
  />
  <label for="size">Size:</label>
  <input
    type="number"
    name="size"
    id="size"
    value="<%= listing.size %>"
    min="0"
  />
  <button type="submit">Edit Listing</button>
</form>
```

Note that we access the `listing` object to supply values for each input.

For now, our form doesn't submit the data to a route - we'll handle that next!
