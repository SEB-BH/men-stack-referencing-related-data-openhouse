<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Add Route Middleware</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create middleware to test authentication and pass user data to rendered views.

## Authentication middleware - protecting listings routes

We are still on the user story:

> As a user, I want to create listings for my real estate properties, to advertise them to potential buyers or renters.

There is the last prep item to tackle before we can create a listing. We need to add some middleware to our application to protect this route from users who are not logged in, and set up an object to help with our authorization when we get to our edit and delete functionality.

Let's protect this route from guest users (users who need to be signed in)by adding a middleware function. Create a new directory in the root of the application called `middleware`. Inside this directory, create a new file called `is-signed-in.js.`

Inside of `is-signed-in.js` let's create a middleware function that checks if a user is signed in. If they are, we will call `next()` to move on to the following middleware function. If they need to be signed in, we will instead redirect them to the sign-in page.

First, we need a function that will accept the `req`, `res`, and `next` parameters. This is the standard Express signature for middleware functions. We are going to use the function name of `isSignedIn`:

```js
// middleware/is-signed-in.js

const isSignedIn = (req, res, next) => {};
```

Next, we need to handle if a user is signed in or not. If we look back over in `server.js`, we can see that we have saved the currently signed-in user to the `req.session` object. This is wonderful, because now we can use an `if` condition to check if the user is signed in. If they are, we will call `next()` to move on to the following middleware function:

```js
// middleware/is-signed-in.js

const isSignedIn = (req, res, next) => {
  if (req.session.user) return next();
};
```

Now, we just have to handle the case of a user needing to be signed in, in which case we want to redirect them to the sign-in page. We can do this with the `res.redirect()` method. We will pass in the path to the sign-in page as an argument to `res.redirect()`:

```js
const isSignedIn = (req, res, next) => {
  if (req.session.user) return next();
  res.redirect('/auth/sign-in');
};
```

If there is a `req.session.user`, the function returns and invokes the next function in the middleware pipeline. Otherwise, `res.redirect()` is invoked, sending the user to the sign in page.

Now that we've created our middleware function, we need to export it to use in our application. At the bottom of `is-signed-in.js` let's export our middleware function:

```js
// middleware/is-signed-in.js

module.exports = isSignedIn;
```

Over in `server.js`, let's import our middleware function. Then, let's use the `isSignedIn` middleware function to protect our listings routes. We can do this by adding it as the second argument to the `app.use()` method call for our listings controller:

```js
// server.js

// other require statements for libraries above

// require in middleware
const isSignedIn = require('./middleware/is-signed-in.js');

const authCtrl = require('./controllers/auth.js');
const listingsCtrl = require('./controllers/listings.js');

// . . .

app.get('/listings', listingsCtrl.index)
// add middleware to protect listings routes
app.get('/listings/new', isSignedIn, listingsCtrl.showNewForm)
app.post('/listings', isSignedIn, listingsCtrl.create)
```

## Authorization middleware - listing owner

Next, let's set up an object to help with authorization for when we get to our edit and delete functionality. We know we can access the currently signed-in user on the `req.session` object. This will let us assign the currently signed-in user to the `res.locals` object. Passing user data to `res.locals` will allow us to access the information in views, for dynamic visuals like saying hello to the user.

To do this, we need to create a new middleware function that will assign the signed-in user to the `res.locals` object. Let's create a new file in our `middleware` directory called `pass-user-to-view.js`. Inside `pass-user-to-view.js` create a middleware function that follows the standard Express signature for middleware functions. We are going to use the function name of `passUserToView`:

```js
// middleware/pass-user-to-view.js

const passUserToView = (req, res, next) => {};
```

We know we want to assign the current signed-in user to the `res.locals` object, but what if there is no signed-in user? We'll need to handle this case as well. Since we want to preform an action based on a boolean condition, we can use a ternary expression to accomplish our task. We will assign the current signed-in user to the `res.locals` object if there is a signed-in user, else we will assign `null` to the `res.locals` object:

```js
const passUserToView = (req, res, next) => {
  res.locals.user = req.session.user ? req.session.user : null;
  next();
};

module.exports = passUserToView;
```

> 🚨 Don't forget to use `next()` to move to the following action in our Express application and to export our middleware function!

Success! We now have a middleware function that will assign the current signed-in user to the `res.locals` object so that we can access the `user` in our views. Let's import our middleware function into `server.js` and use it to assign the current signed-in user to the `res.locals` object:

```js
// server.js

// other require statements for libraries above
const isSignedIn = require('./middleware/is-signed-in.js');
// require in middleware
const passUserToView = require('./middleware/pass-user-to-view.js');

// Router/Controller requires statements and database connection

app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true,
  })
);

// add passUserToView middleware function to Express application
app.use(passUserToView);

app.get('/', (req, res) => {
  res.render('index.ejs', {
    user: req.session.user,
  });
});
```

> 💡 Using the `next()` method in Express is a way to move to the following action in our Express application. After we have assigned the current signed-in user to the `res.locals` object, we want to move on to the next action in our Express application. In this case, that is the `app.get()` method call for the root route.
