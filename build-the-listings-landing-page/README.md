<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build the Listings Landing Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to construct a landing page for a resource in a MEN stack application.

## Route for `GET` all listings

Before creating a listing, we will need a route to display all of the listings. This way, we can redirect users to this listings page after they create a new listing.

We can determine that we need a `GET` request to `/listings`. This will be our index route for listings:

| Action | What It Does          | HTTP Verb | Route       |
| ------ | --------------------- | --------- | ----------- |
| Index  | Displays all listings | `GET`     | `/listings` |

> 💡 The index action is one of two read actions in CRUD. It is used to display all of the resources of a given type.

## Rendering the listings index page

We have confirmed that our route is working; now it's time to render the listings index page. For right now, we are not going to worry about displaying any data, we just want to set up a view that we can add to in the future. Let's create a new directory called `listings` in our `views` directory. Inside of `views/listings`, create a new file called `index.ejs`. Inside of `index.ejs`, let's add the following code:

```html
<!-- views/listings/index.ejs -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>All Listings</title>
  </head>
  <body>
    <h1>Here's all the Listings</h1>
  </body>
</html>
```

Now that we have an index page for our listings, let's render this page in our controller. Inside of `controllers/listings.js`, update the controller to render the new listings index page.


Now that we have our listings page, let's include an anchor element in our `listings/index.ejs` file to link to the form for creating a new listing:

```html
<!-- listings/index.ejs -->

<!--Meta Data located above-->
<h1>Here's all the Listings</h1>
<!--Add in new anchor element-->
<a href="/listings/new">Add a New Listing</a>
<!--Closing body and html element under-->
```