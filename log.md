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