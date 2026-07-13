<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build Create Functionality</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to build `create` functionality for a resource in a MEN stack application.

## `POST` a new listing

The action for creating a new listing breaks down into two parts:

- Rendering a form for the user to enter the listing details.
- Using the form data to create the listing in the database.

In the last section, we completed the first of the above tasks to create a new listing. Now, we are going to focus on the second task. We want to use the form data to create a new listing in the database. Since we are accepting data from the user, we know that we need a `POST` route to `/listings`. Let's add this route to our routes table:

| Action | What It Does                            | HTTP Verb | Route           |
| ------ | --------------------------------------- | --------- | --------------- |
| Index  | Displays all listings                   | `GET`     | `/listings`     |
| New    | Displays a form to create a new listing | `GET`     | `/listings/new` |
| Create | Creates a new listing in the database   | `POST`    | `/listings`     |

> We carried over the index and new routes from the previous section to build onto this routes table.

Now we know what our route should be, let's update our form in `views/listings/new.ejs` to send a `POST` request to `/listings`:

```html
<!-- views/listings/new.ejs -->

<form action="/listings" method="POST"></form>
```

## 🎓 You Do: Create a new listing

We have determined that we need a route for `POST` requests to `/listings`. Let's build it! Inside of `controllers/listings.js` do the following steps:

1. Create a route for `POST` requests to `/listings`.
2. `console.log()` the data being sent from the form.
3. Using `res.redirect()` redirect to `/listings`.

Test by submitting the form in the browser.

> 🚨 Make sure to use `req.body` to access the data from the form.

After you have completed the above steps, you should be able to see the data from the form in the terminal. You should also be redirected to `/listings` in the browser.

## Database insertion

We have the form data! However, we still need one last piece of the puzzle. If we look at our schema again, we see the field `owner`. We intentionally left that out of the form, because we don't want the user to be able to set the owner of a listing. Instead, we want the currently signed-in user to be 'automatically' assigned to a listing they create.

Recall that we can access the currently signed-in user on the `req.session` object. We can use this to set the owner of the listing in our controller function. Let's update our route to create a new listing in the database:

```js
router.post('/', async (req, res) => {
  req.body.owner = req.session.user._id;
  await Listing.create(req.body);
  res.redirect('/listings');
});
```

> 💡 If we were to `console.log` the `req.body` object after we add the `owner` key, we would see an object that directly matches our schema. Since this is the case, we can pass the `req.body` object directly into the `Listing.create()` method.
