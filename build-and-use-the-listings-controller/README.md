<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build and Use the Listings Controller</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to require and mount a controller file.

## Building the listings controller

We are still on the user story:

> As a user, I want to create listings for my real estate properties, to advertise them to potential buyers or renters.

However, we need to do some more prep work before we can create a listing. We have our model now, so let's create our controller. The goal here is to create a router, import the listings model, and export the router so that we can mount it in our `server.js` file.

Let's create a new file in our `controllers` directory called `listings.js`. Inside `listings.js` let's set up our functions, require our listing model, and export our functions:

```js
// controllers/listings.js

const Listing = require('../models/listing')

const index = async (req, res) => {
  res.send('All listings')
}

const showNewForm = async (req, res) => {
  res.send('New listing form')
}

const create = async (req, res) => {
  res.send('Create a listing')
}

module.exports = {
  index,
  showNewForm,
  create,
};
```

Once our controllers are exported, we can import them into our `server.js` file and use them:

```js
// server.js

// other require statements for libraries above
const authCtrl = require('./controllers/auth.js')
// add listings controller:
const listingsCtrl = require('./controllers/listings.js')


// listings routes
app.get('/listings', listingsCtrl.index)
app.get('/listings/new', listingsCtrl.showNewForm)
app.post('/listings', listingsCtrl.create)
```

Great, our controller file is set up. Time to add middleware!
