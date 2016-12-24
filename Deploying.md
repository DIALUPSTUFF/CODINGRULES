# Deploying Guidelines

###Staging Deployment Process
Our staging site is hosted on Heroku, and can be reach at https://dialupstuff.herokuapp.com/.

**Preparation**:
If you haven't already, download [Heroku's CLI](https://devcenter.heroku.com/articles/heroku-command-line). Afterwards, run 'heroku login', and enter in the login info. 

**How It Works**:
Our Heroku server can be treated like a remote repository. Similar to Github, we can `git push` code to the Heroku server. Any time we push to our Heroku server, it will essentially wipe all the code it previously stored, store the new code that was pushed, run `npm install`, and then use the command listed in our Procfile to start the project's server.

**Steps**:

1. Switch to the `staging` branch and merge in your development branch.
2. Add a new remote for Heroku to your local Git repo by running `heroku git:remote -a dialupstuff`.
3. Run `git push heroku staging:master`. This will push the contents of your local `staging` branch to the `master` branch on the Heroku server.
4. Watch the build process for errors. Run `heroku logs` to ensure that the server started, or to see what specifc errors occured.
5. Use `heroku open` to open the webpage for the site and see it live on staging.


###Production Deployment Process
Our production site is hosted on Digital Ocean, and can be reach at http://dialupstuff.com/.

**How It Works**:
Our Digital Ocean server can be thought of as a separate computer. Similar our own dev environments, we can `git pull` code from our remote repository on Github and start it. We simply need to pull the code onto the Digital Ocean server, download depencies and start the Node server code. We use [Forever](https://github.com/foreverjs/forever) to keep the Node server for this project running...well...forever.

**Steps**:

1. Login to the Digital Ocean account and copy the IP address of the droplet.
2. Open a terminal window, and SSH into the droplet using `ssh root@<ipaddress>.
3. Once you're in, use `cd /opt/DialUpSite`. 
4. Switch to the prod branch with `git checkout prod`, and pull new changes with `git pull`. 
5. Run `npm install` to download any missing dependencies.
6. Run `forever restart server.js` to start the node server for this project. You may need to change the Express server to use port 80 if the code doesn't pick up on the correct port using the environment variable. We will looking into correcting this.
7. Ensure that the server is running by checking `forever list` and open Forever logs with `forever logs server.js` to check ouput.
8. If all seems good, updates to the site should now be officially deployed.

