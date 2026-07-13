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

## 🎓 You Do: Log all listings

We have determined that we need a route for `GET` requests to `/listings`. Let's build it! Inside of `controllers/listings.js` do the following steps:

1. Create a route for `GET` requests to `/listings`.
2. Using the `Listing` model, find all listings.
3. `console.log()` the listings.
4. `res.send()` a message that says "Listings index page".
5. `console.log()` any errors that occur.
6. Test your route in the browser by going to `localhost:3000/listings` manually.

> 🚨 Make sure you have started your server with `nodemon` before testing your route.

After completing the above steps, you should see the message "Listings index page" in the browser. You should also see an empty array in the terminal. This is because we have yet to create any listings.

Make sure you have debugged your application before moving on!

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

Now that we have an index page for our listings, let's render this page in our route. Inside of `controllers/listings.js`, update the route to render the new listings index page.
