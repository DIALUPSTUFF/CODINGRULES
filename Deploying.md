# Deploying Guidelines

###Staging Deployment Process
Our staging site is hosted on Heroku, and can be reach at https://dialupstuff.herokuapp.com/.

**Preparation**:
If you haven't already, download [Heroku's CLI](https://devcenter.heroku.com/articles/heroku-command-line). Afterwards, run 'heroku login', and enter in the login info. 

**How It Works**:
Our remote Heroku server can be treated like a remote repository. Similar to Github, we can `git push` code to the Heroku server. Any time we push to our Heroku server, it will essentially wipe all the code it previously stored, store the new code that was pushed, run `npm install`, and then use the command listed in our Procfile to start the project's server.

**Steps**:

1. Switch to the `staging` branch and merge in your development branch.
2. Add a new remote for Heroku to your local Git repo by running `heroku git:remote -a dialupstuff`.
3. Run `git push heroku staging:master`. This will push the contents of your local `staging` branch to the `master` branch on the Heroku server.
4. Watch the build process for errors. Run `heroku logs` to ensure that the server started, or to see what specifc errors occured.
5. Use `heroku open` to open the webpage for the site and see it live on staging.
