<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Instructor Guide</span>
</h1>


# Lesson Flow

> "Today we're planning our first real software project. We'll use Open House as our example, then you'll make the same decisions for your own project."

# Part 1 — What are we building?

Start with almost no code.

Just show them the finished app.

Ask:

> If I asked you to build this from scratch tomorrow... where would you even begin?

Let them answer.

Someone will say:

- Login
- Houses
- CSS
- Database
- Navbar

Perfect.

Then introduce Scrum.

> Good developers don't try to build everything at once.

> They break big projects into small pieces.

Draw something like:

```
Open House

Authentication
Listings
Viewing one listing
Editing listings
Deleting listings
Styling
Validation
```

Then immediately have students do the same.

## Student Activity #1

Give everyone five minutes.

Write:

> Break your own project into 5–10 major features.

Example

Restaurant Finder

```
Authentication
Restaurants
Reviews
Favorites
Profile
Search
Styling
```

or

Recipe App

```
Authentication
Recipes
Ingredients
Categories
Favorites
Search
```

No coding.

Only planning.

# Part 2 — User Stories

Now explain that features are still too big.

Developers write User Stories.

Example:

Authentication

Instead of

> Build authentication

write

- As a visitor I want to create an account.
- As a user I want to sign in.
- As a user I want to sign out.

Listings

- As a visitor I want to see all listings.
- As a user I want to create a listing.
- As a user I want to edit my listing.
- As a user I want to delete my listing.


## Student Activity #2

Convert their features into user stories.

Require three stories for every feature.


# Part 3 — Prioritization

Now ask:

Can we launch Open House with only these?

```
Create account

Sign in

View listings

Create listing
```

Yes.

Do we NEED?

```
Favorites

Searching

Filtering

Dark mode

Maps
```

No.

Explain MVP.

Then create three columns.

```
Must Have

Should Have

Could Have
```

Students sort their stories.

This is Scrum without calling it complicated names.


# Part 4 — Estimation

Don't use story points.

Keep it simple.

Ask:

Is this...

🟢 Small

🟡 Medium

🔴 Large

Example

```
Sign In

🟢
```

```
Delete Listing

🟢
```

```
Image Uploads

🔴
```

```
Password Reset

🔴
```

Students estimate their own stories.


# Part 5 — Sprint Planning

Now build Open House.

Ask:

We have one week.

What do we build first?

Students usually answer:

Authentication

Great.

Authentication becomes Sprint 1.

Then:

```
Sprint 1

Sign Up

Sign In

Sign Out
```

Sprint 2

```
Listings

Create

Index

Show
```

Sprint 3

```
Edit

Delete

Styling
```

Students make a sprint plan for their projects.


# Part 6 — Kanban Board

Now actually create one.

```
Backlog

To Do

Doing

Done
```

Start moving cards.

This becomes really engaging.


# While Building Open House

Every time you finish something...

Move it.

```
✔ Sign Up
```

moves into

Done

Students immediately understand why Scrum exists.


# Where to introduce the code

Only after planning.

Then say

> Great. Now that we know WHAT we're building...

> Let's start building Sprint 1.


# While Coding

I would constantly relate code back to planning.

Example

```
Sprint 1

✔ Sign Up
```

Today we're writing

```
User model
```

because that's needed for

```
Create Account
```

Later

```
Controller
```

because that's needed for

```
Sign Up
```

Later

```
View
```

because that's needed for

```
Registration page
```

Students begin seeing architecture instead of random files.


# End the lesson with this

By the end of the day, every student leaves with:

- ✅ A feature list
- ✅ User stories
- ✅ An MVP
- ✅ Prioritized backlog
- ✅ A simple sprint plan
- ✅ A Kanban board
- ✅ A clear understanding of how Open House will be built
- ✅ The first controller and routes for Sprint 1
