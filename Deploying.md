# Deploying Guidelines

### Staging Deployment Process
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


### Production Deployment Process
Our production site is hosted on Digital Ocean, and can be reach at http://dialupstuff.com/.

**How It Works**:
Any time someone pushes to the `prod` branch, our Docker images [repository on Docker Hub](https://hub.docker.com/r/bomanimc/dialupsite-docker/) builds a new that matches the code that has just been pushed. On our Digital Ocean server, a service called [Watchtower](https://github.com/v2tec/watchtower) pulls down the latest image on the repository every 5 minutes, and compares it to the image that is currently being run on the server. If it's different, it gracefully shutdowns the container running the old image, and starts a new one using the new image. configured using `docker-machine`, and runs a Docker container for our project. In short, pushes to the `prod` branch result in live changes to the website in about 5 minutes.

**Steps**:

1. Merge code from `staging` (or another branch) to `prod`.


### Docker + Digital Ocean Machine Management Process (Experimental)
For those curious about how to configure this Docker + Digital Ocean setup.

**Steps**:

1. Download the [Docker Toolbox](https://www.docker.com/products/docker-toolbox), and verify that it's successfully installed [using this guide](https://docs.docker. com/engine/getstarted/step_one/). Once the toolbox has started, ensure you've open the Docker application (Docker daemon) that should now be present on your computer.
2. Run `eval "$(docker-machine env default)"` to configure your local CLI to utilize `docker-machine` commands. More details on why this is necessary can be found [here](http://stackoverflow.com/questions/40038572/eval-docker-machine-env-default).
3. `cd` into the root directory of your project and run `docker build -t <image_name> .` to create an [image](https://docs.docker.com/engine/getstarted/step_two/) of the project on your system. Check that the image was successfully by running `docker images`.
4. Do a test run of the image using `docker run --name <name> -p 8080:3000 -d <image_name>`. This starts a Docker container that is running the image of the project we just created. Since this container is actually a virtual machine, we need to forward the exposed container port (3000) to our system's host port (8080, as specified) so that we can pull up the project with localhost. You can view the project at http://localhost:8080/ to verify that it is working correctly.
5. In a separate terminal tab, run `docker stop <name>` to stop the container. Afterwards, remove the container with `docker rm <name>`. Use `docker ps -a` to ensure that the container has now been removed.
6. Setup an Automated Build project on [DockerHub](https://hub.docker.com). Link this to your GitHub account, and configure the `prod` branch to build with Docker tag `latest` in Build Settings. Save changes and trigger a build to test.
7. Once the build is complete, reconfigure your local `docker-machine` CLI to utilize point at the Digital Ocean Docker machine using `eval $(docker-machine env <machine_name>)`.
8. Pull the image produced by the Automated Build on Docker Hub onto the Digital Ocean Docker Machine using `docker pull <docker_repo_name>`.
9. Run `docker run --name <name> -p 80:3000 -d <image_name>`. This will start a Docker container on the Digital Ocean machine that will run the specified image. At this point, the project should be up and running publicly. Check the public URL (the server's IP address or the domain name) to verify.
10. Next, install [Watchtower](https://github.com/v2tec/watchtower) by running `docker pull v2tec/watchtower`.
11. Start Watchtower with...
```
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  v2tec/watchtower --cleanup
```
12. That's it. This will automatically check for new versions of images that are being used in active containers running on the server. The `--cleanup` flag tells Watchtower to remove old versions of the image when it sees a new one.
