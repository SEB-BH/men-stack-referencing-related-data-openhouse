<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Build the Navigation Bar Partial</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement a partial in a MEN stack application.

## Navigation bar

Soon, we will need to build a few more routes to handle the creation of a listing. Let's first create a navigation bar so that we can easily navigate between the different pages of our application.

We are going to be leveraging partials to build our navbar. Partials are a way to break up our views into smaller pieces, which helps keep our code DRY and make it easier to maintain. Create a new directory called `partials` in the `views` directory. Inside `partials`, create a new file called `navbar.ejs`. Inside of `navbar.ejs`, let's create the HTML for our navbar that includes anchor tags for the following routes:

- Sign Up
- Sign In
- Sign Out
- Listings

```html
<!-- partials/navbar.ejs -->

<nav>
  <a href="/listings">View all Listings</a>
  <a href="/auth/sign-out">Sign Out</a>
  <a href="/auth/sign-up">Sign Up</a>
  <a href="/auth/sign-in">Sign In</a>
</nav>
```

The application should only show navbar links relevant to the user. For example, if a user is signed in, we do not want to show the sign up and sign in links, nor do we want guest users to be able to view listings. Remember that in our middleware step we sent the current user to our views using the `res.locals` object. We can use this to conditionally render our navbar links.

Back in our `navbar.ejs` file, let's add some logic to conditionally render our navbar links:

```html
<!-- partials/navbar.ejs -->

<nav>
  <% if (user) { %>
  <a href="/listings">View all Listings</a>
  <a href="/auth/sign-out">Sign Out</a>
  <% } else { %>
  <a href="/auth/sign-up">Sign Up</a>
  <a href="/auth/sign-in">Sign In</a>
  <% } %>
</nav>
```

## Add navbar to listings index page

Next, let's add our new navbar partial to our listings index page. Inside of `views/home.ejs`, include the navbar partial. 

```ejs
<%- include('./partials/navbar') %>
```

Test this by manually going to `localhost:3000/` in the browser.

> 🚨 Make sure to use the correct path to the navbar partial.
