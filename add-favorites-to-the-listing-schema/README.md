<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Add Favorites to the Listing Schema</span>
</h1>

**Learning objective:** By the end of this lesson, students will understand how to add a many-to-many relationship in MongoDB.

## User story

Take a look at the two user stories below:

> As a user, I want to see the number of favorites a listing has received, to gauge its popularity and potential demand.
> As a user, I want to favorite a listing, so I can easily find and review it later.
> As a user, I want to remove listings from my favorites, to keep my favorites list relevant to my current interests.

To implement this functionality, we'll first need to add a new property to our `listingSchema` called `favoritedByUsers`.

The `favoritedByUsers` property will facilitate a _many-to-many relationship_ between listings and users.

## What is a many-to-many relationship?

In a many-to-many relationship, multiple documents in one collection are associated with multiple documents in another collection. This kind of relationship is useful when multiple users need to be associated with a resource and vice versa. For example, a user can visit many places, and each place has been visited by many users.

Our application currently has a **one-to-many** relationship between listing and user, where one user can own many listings. However, our favoriting functionality will require a **many-to-many** relationship, as a listing can be favorited by many users, and a user can favorite many listings.

## Implementing a many-to-many relationship

The `favoritedByUsers` property will hold an array of references to documents in the `User` collection. Whenever a user marks a listing as a favorite, we'll add their `ObjectId` to this array.

Update your `listingSchema` with the following:

```js
// models/listing.js

const listingSchema = new mongoose.Schema({
  // ... other properties above

  favoritedByUsers: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
    },
  ],
});
```

There are two benefits to this setup. First, if we ever want to see how many people have favorited a listing, we can access the `length` of the `favoritedByUsers` array. Second, by checking the array for a specific `ObjectId`, we'll be able to determine whether a specific user has favorited the listing or not.
