# Deployment for Node-Mongo-Express: Heroku & Atlas

## Learning Objectives

- Define deployment
- Describe the difference between development, test, and production environments
- Use environmental variables to keep sensitive data out of code
- Set up a Mongoose database using Atlas
- Deploy a Node App using Heroku

## About Deployment

### What is Deployment?

Deployment is the act of putting an app up on one or more internet-connected
servers that allow users to access and use the app.

_Question:_ What changes in an application when it is deployed?

### Requirements for Deployment

There are generally a few things we need for an app to be properly deployed:

- **Server** - the server(s) must be on and connected to the internet
- **Executable Code** - we must get our code onto the server and be able to run
  it
- **Dependencies** - the server(s) must have the proper dependencies installed
- **Services** - the server(s) must be running the correct services (web,
  database, email, etc.)
- **Configuration** - we must configure our running app with respect to its
  deployment environment


## Environments and Environmental Variables

### Environments

Application environments are an important part of the context in which an
application runs. Each environment is configured to support a certain usage of
the application.

Typical application environments include:

- **Development** - environment where an app or new feature(s) are created and
  run locally
- **Production** - environment for complete and tested code to be hosted online
  for clients to use

So far, we've been using the `development` environment by default. Today we'll
look at deploying to the `production` environment, a.k.a. the public, published
version of our site. Configuration settings often include...

- The name of the database
- The username/password to connect to the database

## Deploying Node-Express-Mongoose Applications

Deploying our Node-Express-Mongoose application consists of 2 sets of steps.
First, we'll sign up for Heroku, download the Heroku CLI, and set up a Mongo
database that our app can connect to. Next, we'll configure our
Node-Express-Mongoose applications to connect to this new cloud-hosted database
and finally, deploy our application to Heroku.


### Heroku

We'll be using a service called Heroku to deploy our apps, because it makes all
the steps for deployment easy, simplifying and expediting the process. For
example, Heroku automatically does the following...

- Starts up a new server when we run `heroku create`, and installs all the
  necessary services
- Adds a new remote to our Git repo, so we can just run `git push heroku master`
  to copy our code over
- Detects our database
- Detects the language our program is written in and chooses a buildpack
- Automatically installs our app's dependencies, and starts our app
- Easily change configuration information using `heroku config`

Heroku also has a FREE pricing tier!

### Mongo Atlas

[Mongo Atlas](https://cloud.mongodb.com) is a cloud-based database service that
hosts a protected MongoDB instance that you can easily integrate with an
application deployed on Heroku.

Today, you will need to set up an Atlas account and database to host your database. You will use the app assigned by the instructional team. 

### Deployment in 19 Easy Steps

Deployment is essentially an exercise in following directions. Follow the
step-by-step instructions below to deploy your App. Pay attention to
the notes following each prompt! Fully read each prompt (including the notes)
before executing each step.

#### Set up Heroku

1. Sign up for a free [Heroku](https://www.heroku.com/) account.

2. Follow the instructions
   [here](https://devcenter.heroku.com/articles/heroku-cli) to download the
   Heroku CLI.

   > NOTE: In your terminal, you will run the command
   > `brew tap heroku/brew && brew install heroku`

3. From your project directory, create an app on Heroku.

 ##### NOTE:

 - Make sure you are in the app project directory before you run
   this command

 - `heroku create` prepares Heroku to receive your code. Heroku will
   randomly create an app name for you if you don't specify one. You may need
   to login before you can create an app.

 - If you choose to name your application, you will need to use a unique
   name (something someone else has not used before). If the name is taken,
   Heroku will prompt you to choose something new.

In the below example the app name was `bookmarks-app-heroku` and so heroku creates that app and provides both the site and github urls.
 
   ```bash
   $ heroku create <your-app-name>
   
   # Example
   $ heroku create bookmarks-app-heroku
    Creating ⬢ bookmarks-app-heroku... ⣾ 
    Creating ⬢ bookmarks-app-heroku... done
    https://bookmarks-app-heroku.herokuapp.com/ | https://git.heroku.com/bookmarks-app-heroku.git
   ```

#### Set up MongoDB Atlas

4. Go to [Mongo Atlas](https://cloud.mongodb.com) and sign up for an account, or
   sign in if you have one already.

5. In the sidebar on the left, open the dropdown menu title `context` and select
   `New Project`. Give it a name and click `Create Project`. You can leave the
   defaults in the "Create a Project" page and hit the "Create Project" button.

   ![Create a project](https://i.imgur.com/BMiQoMW.png)

6. Finish creating your new project and click the `Build a Cluster` button. **Be
   sure to select the free option for building your cluster!** Otherwise, leave
   all the default settings. Your cluster could take a few minutes to finish
   building.

   ![Build a cluster](https://i.imgur.com/PIeTiRJ.png)

7. When your cluster is finished, click the "Connect" button.

8. Add `0.0.0.0/0` for the whitelisted IP address, If you just add the current
   IP address you can click on "Network Access" in the sidebar, then the "Add IP
   Address" button in the top right, and finally "Allow Access from Anywhere"
   and click Confirm.

##### NOTE:

- If you forget to whitelist the IP, go to "Network Access" under "Security" on
  the left sidebar. Next select "Add IP Address" in the top right corner, and
  there should be a "Allow Access from Anywhere" button (or you can enter
  `0.0.0.0/0` manually).

9. Also create a username and password. **Remember the username and password you use for your database, you'll need them in a later step!**

   Go to "Database Access" under "Security" on the left sidebar. Then hit the
   "Add New User" button on the top right.

   ##### NOTE:

   - This is **not** the user with which you logged in to Atlas. "User" refers
     to an app that has access to your database, and **not your Atlas account/username**.

   - Create a Database username and Database password that you will remember, or
     write it down somewhere. You will need this information again later.

   - Do not use any special characters! Special characters can complicate the
     process when configuring your Atlas database with Heroku.

   - Do not check 'Make read-only'. Full CRUD functionality will not work with a
     read-only database. Making this user admin is perfect!

#### Heroku & Node Setup

Next we need to let our Node app know _when_ to use Mongo Atlas as our database,
and when to use our local DB.

A built-in environment variable, `NODE_ENV` available under
`process.env.NODE_ENV`, is used to define the application environment. When a
Node app is deployed to Heroku, Heroku automatically sets the `NODE_ENV`
variable to `'production'`.

The code below is stating that we should use the Mongo Atlas URI (in other
words, the link that connects us to the Atlas database) when in production, and
the local database at all other times.

10. In your Node app's `db/connection.js` file, we want to use the environment
    to determine the `MONGODB_URI` for our application to connect to.

    ```sh
    const MONGODB_URI = "mongodb://localhost/your-db-name"
    let MONGODB_URI = "";
    ```

    Then, add

    ```js
    if (process.env.NODE_ENV === "production") {
      MONGODB_URI = process.env.DB_URL;
    } else {
      MONGODB_URI = "mongodb://localhost/your-db-name";
    }
    ```

    The `mongoose.connect` method will stay the same.

11. Next, you will need to make a minor change to `server.js`. When Heroku starts
    your app it will automatically assign a port to `process.env.PORT` (an
    environmental variable!) to be used in production. We can modify
    `app.listen` to accommodate Heroku's production port and our own local
    development port. Add the following:

    ```js
    app.set("port", process.env.PORT || 8080);

    app.listen(app.get("port"), () => {
      console.log(`✅ PORT: ${app.get("port")} 🌟`);
    });
    ```

12. Heroku looks for instruction when starting your app. In this case, that
    instruction is to run `node server.js`. In `package.json` under `scripts`,
    add the following...

    ```sh
    "scripts": {
      "start": "node server.js"
    }
    ```

13. Add and commit all changes we've made to the app. It is a good idea to
    push to GHE as well. 

#### Heroku & Atlas Configuration

14. Go back to your Atlas database in your browser. Click on "Connect" and then
    the "Choose a Connection Method" button, then "Connect Your Application",
    then "Short SRV Connection String". **Copy the connection string!**

    ![](https://i.imgur.com/aaz0PgV.png)

    ##### Note:

    - You must copy this from your own database to capture your unique database
      id numbers.

    - You will still need to manually substitute the `USERNAME` and `PASSWORD`
      with the one you created in the next step.

15. Set the URI you just copied as an environment variable called `DB_URL` using
    `heroku config:set`, filling in the `<USERNAME>` and `<PASSWORD>` you
    created on the "Users" page. Run the following in your terminal (in your
    project's folder)...

    ```bash
    ❯ heroku config:set DB_URL="mongodb+srv://dbadmin:dbadmin@cluster0-onk2v.mongodb.net/test?retryWrites=true&w=majority" 
      Setting DB_URL and restarting ⬢ bookmarks-app-heroku... ⣾ 
      Setting DB_URL and restarting ⬢ bookmarks-app-heroku... done, v6
      DB_URL: mongodb+srv://dbadmin:dbadmin@cluster0-onk2v.mongodb.net/test?retryWrites=true&w=majority
  
    # CONFIRM
    $ heroku config
    ```

    ##### Note:

    - Do not copy the above command. The database id numbers in the URI should
      match the URI that you copied from your own Atlas database. The **sample**
      command above includes id numbers of `012345` and `12345`. Does your own
      Atlas URI match this? Probably not.

    - Your database name will be included in the URI you copied from your Atlas
      database. You will need to manually add the `USERNAME` and `PASSWORD` that
      you created in Step 10. **DONT FORGET TO PUT QUOTES AROUND THE URL PART**

    - Assigning environmental variables using `heroku config:set` is very
      similar to using `export`, the difference being accessibility. Variables
      assigned using the heroku command are only accessible from the production
      app deployed on heroku.

#### Deploying to Heroku

16. Push your code to Heroku remote. 

    - If you are deploying to Heroku from the master branch, you can run the
      command `$ git push heroku master`.

17. Seed your Atlas database by running the command:

    ```bash
    $ heroku run node db/seed.js
    ```

    ##### Note:

    - `heroku run` allows you to run js files on the heroku server. We can seed
      our database on heroku using the same seed file we used locally.

18. Open your application! Run the command `$ heroku open` in your terminal.
    This will launch your production app in a new browser tab.

19. You just successfully deployed your first app! You should be proud, so pat
    yourself on the back, give your neighbor a high five, call your parents, and
    share this milestone with someone you love!

## Solving Deployment Issues

**Not working?** Don't worry! Debugging is a part of your life now. Check out
these tips on solivng deployment issues.

### Google is Your Best Friend

More often than not, solving deployment issues requires a good deal of Googling.
Don't expect to find a silver bullet -- often we must go through many different
issues other users may have encountered to understand our own.

What should you Google?

- If you aren't able to deploy, Google the error that shows up in your terminal
  after trying to push your app.
- If you are able to deploy but your app doesn't load/function properly, see
  what shows up after running `$ heroku logs --tail` in the terminal.

### Heroku Errors v. Node Errors

You may notice that the errors you receive after running `$ heroku logs --tail`
are not always detailed or particularly helpful.

- Instead of focusing on the Heroku errors, pay attention to the Node errors in
  your terminal. They will often provide you with more direction.
- Still stuck? Check out the
  [Heroku Error Codes Documentation](https://devcenter.heroku.com/articles/error-codes).

#### Note:

- A common error that students come across is file name case sensitivity. Check
  out
  [this documentation](https://stackoverflow.com/questions/10523849/changing-capitalization-of-filenames-in-git)
  on changing the capitalization of filenames in Git.

### Help Each Other Out!

If you notice somebody running into the same problem as you, try working
together on debugging it!
