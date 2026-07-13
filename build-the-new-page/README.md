<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build the New Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to construct the `new` page containing a form for new listings in a MEN stack application.

## `GET` a new listing form

The action for creating a new listing breaks down into two parts:

- Rendering a form for the user to enter the listing details.
- Using the form data to create the listing in the database.

These two parts require separate routes. Let's start to think through the first action. We want to render a form for the user to enter their listing details. From this, we can determine that we need a `GET` route, but we have already mapped a `GET` route to `/listings` to view all listings. Let's add `/new` to the end of the path to create a new unique route for this action.

| Action | What It Does                            | HTTP Verb | Route           |
| ------ | --------------------------------------- | --------- | --------------- |
| Index  | Displays all listings                   | `GET`     | `/listings`     |
| New    | Displays a form to create a new listing | `GET`     | `/listings/new` |

> 💡 We carried over the index route from the previous section to build onto this routes table.

Now we know what our route should be, let's include an anchor element in our `listings/index.ejs` file to link to this new route:

```html
<!-- listings/index.ejs -->

<!--Meta Data located above-->
<h1>Here's all the Listings</h1>
<!--Add in new anchor element-->
<a href="/listings/new">Add a New Listing</a>
<!--Closing body and html element under-->
```

## 🎓 You Do: Render a new `listing` form

We have determined that we need a route for `GET` requests to `/listings/new`. Let's build it! Inside of `controllers/listings.js` do the following steps:

1. Create a route for `GET` requests to `/listings/new`.
2. Using `res.render()`, render a new view called `listings/new.ejs`.
3. Inside of `views/listings`, create a new file called `new.ejs`.
4. Include the navbar partial in `new.ejs`.
5. Add an `<h1>` element that says "Add a New Listing!".
6. Add a form that has a submit button. Leave the form blank for now.

Test using the anchor element in `listings/index.ejs` to navigate to `localhost:3000/listings/new` in the browser.

> 🚨 Make sure to give your submit button text to see it in the browser.

After completing the above steps, you should see the message "Add a New Listing!" in the browser. You should also see the navbar partial and the submit button.

## Schema to form field mapping

We are at the point of accepting some data from the user; however, what should we accept? We can use the schema for our listings to determine what data we want to accept from the user. Let's take a look at our schema fields for listings:

```js
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
```

We can see that we need to accept the following data from the user:

- Street address - String, and is required
- City - String, and is required
- Price - Number, minimum of 0, and is required
- Size - Number, a minimum of 0, and is required

> We intentionally leave off the `owner` field because we will not expect our end users to enter their own user ID. We will instead handle this in the controller action.

We are going to need an input for each of these fields. Let's add these to our form in `views/listings/new.ejs`:

```html
<!-- views/listings/new.ejs -->

<form>
  <label for="street-address">Street Address:</label>
  <input type="text" name="streetAddress" id="street-address" />
  <label for="city">City:</label>
  <input type="text" name="city" id="city" />
  <label for="price">Price:</label>
  <input type="number" name="price" id="price" min="0" />
  <label for="size">Size:</label>
  <input type="number" name="size" id="size" min="0" />
  <button type="submit">Add Listing</button>
</form>
```

> The `name` attribute on the input elements will be used to access the data from the form in our controller action. We are also using some HTML form validation to ensure the user enters a number greater than 0 for the price and size fields.

We have completed the first action of creating a new listing. We have rendered a form for the user to enter their listing details. Next, we need to handle the data sent from the form!
