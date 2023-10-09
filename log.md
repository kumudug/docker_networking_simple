 # Step By Step

 * Build the image using the code 
    - `docker build -t network-simple:first .`
    - Named 'network-simple'
    - Tagged as 'first'
    - Ran in the same folder as the `Dockerfile` thus the `.` as path

* Run a container using the image
   - `docker run --name network-simple-test -d --rm -p 3000:3000 network-simple:first`
   - Running in detached mode
   - Remove after stopping
   - Hardcoded port 
* Ran and exited 
   - since we used detached mode `-d` flas no logs in console
   - since we used `-rm` the container got destroyed after exit
* Ran again to sort out the issue
   - this time in attached mode 
   - `docker run --name network-simple-test --rm -p 3000:3000 network-simple:first`
   - Got the error `Mongoose.prototype.connect() no longer accepts a callback`
* Mongose changed the APIs in the new version
   - Changed the code to fix the issue
   - Build the image again `docker build -t network-simple:first .` (no changes to tags)
   - Ran again `docker run --name network-simple-test --rm -p 3000:3000 network-simple:first`
   - Got the error `MongooseServerSelectionError: connect ECONNREFUSED 127.0.0.1:27017`
   - This is due to the fact I missed the need to have a mongodb running
   - Needs to dockertize mongo
* Creating the network
   - Now that we are spinning up multiple images that needs to communicate we need to create a network
   - `docker network create test-network`
* Mongo has a official docker image [nongo-docker](https://hub.docker.com/_/mongo)
   - Run a container using the official image it in detached mode to be used by the app
   - Run the mongo container in the previously created network
   - `docker run -d --name mongodb --network test-network mongo`
   - Also now we have to change the mongo db connection string to use the docker network
   - When using docker networks we need to start both containers within the same network by using teh `--network` option
   - Then in the connection string we can refer to the container using the name
   - Ex: `mongodb://localhost:27017/swfavorites` now changes to `mongodb://mongodb:27017/swfavorites`
* Building and running the app again
   - Since we changed teh connection string due to the network we need to rebuild the image
   - `docker build -t network-simple:first .`
   - Run a container using this new image
   - `docker run --name network-simple-test -d --rm -p 3000:3000 --network test-network network-simple:first`
   - Now if you use postman you can send a get to `http://localhost:3000/favorites` and see the api working
   - Since in the app.js 3000 is the hardcoded listen port and we are mapping `3000:3000` from internal to external when running the docker container