<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build and Use the Listings Controller</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to require and mount a controller file.

## Building the listings controller

We are still on the user story:

> As a user, I want to create listings for my real estate properties, to advertise them to potential buyers or renters.

However, we need to do some more prep work before we can create a listing. We have our model now, so let's create our controller. The goal here is to create a router, import the listings model, and export the router so that we can mount it in our `server.js` file.

Let's create a new file in our `controllers` directory called `listings.js`. Inside `listings.js` let's set up our router, require in our listing model, and export our router:

```js
// controllers/listings.js

const express = require('express');
const router = express.Router();

const Listing = require('../models/listing');

module.exports = router;
```

Once our router is exported, we can import it into our `server.js` file and mount it:

```js
// server.js

// other require statements for libraries above
const authController = require('./controllers/auth.js');
// add listings controller:
const listingsController = require('./controllers/listings');

// . . .

app.use('/auth', authController);
// mount listings controller
app.use('/listings', listingsController);
```

Great, our controller file is set up. Time to add middleware!
