# Demo App - Developing with Docker

This demo app shows a simple user profile app set up using:

-   `index.html` with pure JS and CSS styles
-   Node.js backend with Express module
-   MongoDB for data storage

All components are docker-based.

## With Docker

### To Start the Application

**Step 1:** Create docker network

```bash
docker network create mongo-network
```

**Step 2:** Start MongoDB

```bash
docker run -d -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  --name mongodb \
  --net mongo-network \
  mongo
```

**Step 3:** Start Mongo-Express

```bash
docker run -d -p 8081:8081 \
  -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
  -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
  --net mongo-network \
  --name mongo-express \
  -e ME_CONFIG_MONGODB_SERVER=mongodb \
  -e ME_CONFIG_MONGODB_URL=mongodb://mongodb:27017 \
  mongo-express
```

> **_Note:_** Creating docker-network is optional. You can start both containers in a default network. In this case, just omit the `--net` flag in `docker run` command.

**Step 4:** Open Mongo-Express from browser

```bash
http://localhost:8081
```

**Step 5:** Create `user-account` database and `users` collection in Mongo-Express

**Step 6:** Start your Node.js application locally - go to `app` directory of project

```bash
cd app
npm install
node server.js
```

**Step 7:** Access your Node.js application UI from browser

```bash
http://localhost:3000
```

## With Docker Compose

### To Start the Application

**Step 1:** Start MongoDB and Mongo-Express

```bash
docker-compose -f docker-compose.yaml up
```

> You can access the Mongo-Express under `http://localhost:8080` from your browser.

**Step 2:** In Mongo-Express UI - create a new database `user-account`

**Step 3:** In Mongo-Express UI - create a new collection `users` in the database `user-account`

**Step 4:** Start Node server

```bash
cd app
npm install
node server.js
```

**Step 5:** Access the Node.js application from browser

```bash
http://localhost:3000
```

### To Build a Docker Image from the Application

```bash
docker build -t my-app:1.0 .
```

> The dot `.` at the end of the command denotes location of the Dockerfile.
