<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Setup</span>
</h1>

## Setup

Open your Terminal application and navigate to your `~/code/ga/lectures` directory:

```bash
cd ~/code/ga/lectures
```

## Cloning the Auth boilerplate

This lecture uses the [`MEN Stack Auth Template`](https://github.com/SEB-14-Bahrain/men-auth-template) as starter code. Doing so allows us to have a connection established to our MongoDB Atlas, add functioning auth for our user model, and install some of the packages we will need for our app build.

Navigate to the [MEN Stack Auth Template](https://github.com/SEB-14-Bahrain/men-auth-template) and clone the repository to your machine:

```bash
git clone https://github.com/SEB-14-Bahrain/men-auth-template.git open-house
```

Note by adding the `open-house` argument we're cloning the specified repo into a directory called `open-house` on our machines.

Next, `cd` into your renamed directory:

```bash
cd open-house
```

Finally, remove the existing `.git` information from this template:

```bash
rm -rf .git
```

> Removing the `.git` info is important as this is just a starter template provided by GA. You do not need the existing git history for this project.


## GitHub setup

To add this project to GitHub, initialize a new Git repository:

```bash
git init
git add .
git commit -m "First commit"
```

Make a new repository on [GitHub](https://github.com/) named `open-house`. 

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/open-house.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.

Open the project's folder in your code editor:

```bash
code .
```

## Install dependencies

Next, you will want to install all of the packages listed in `package.json`

```bash
npm i
```

## Check your `.gitignore`

Once these files are created, be sure that `.env` and `node_modules` are in your `.gitignore` file.  Doing so will prevent those files and directories from being tracked and we can be confident that any data we add there will not be pushed up to GitHub.

```text
.env
node_modules
```

## Create your .env

Lastly, we want to create `MONGODB_URI` and `SESSION_SECRET` to hold values used in our auth logic.  `MONGODB_URI` will connect to your MongoDB Atlas connection string so you will need to establish one for this application.  `SESSION_SECRET` will aid in your auth session logic.

Add a `.env` file to your application and add the following secret keys to your application:

```text
MONGODB_URI=
SESSION_SECRET=
```

You can use this command to generate a complex string for your session secret:

```bash
node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))"
```

Start the server and you are ready for launch. 

```bash
nodemon server.js
```

Happy Coding! 