# Setting up a Node.js development environment with Docker Compose

Docker is a platform that allows developers to run and deploy applications in standalone processes called containers. A container includes all things necessary to run an application, such as the application code, libraries, configuration files, environmental variables, and the runtime system.

Working with Docker containers in development offers the following benefits:

- You can install any dependencies you want without worrying about system conflicts.
- You can run code in an isolated environment, which makes it easier to troubleshoot issues and safely test new features.
- You can share your container with others, allowing them to run your code exactly the way it does on your machine.

In this tutorial, we will learn how to create an  environment for Node.js development inside a Docker container.

# Why use Docker Compose for development?

One way to create a container is by using a container image. An image is a template that defines a container.

However, images typically define containers used in production. They are not supposed to contain development-related commands, so for our environment this is not ideal. 

Instead, we will create and run our container using Docker Compose, which is the Docker's native tool for managing multi-container applications without creating an image. 
# Prerequisites

To follow this tutorial, you will need:

- Basic knowledge of Node.js and npm.
- Docker Desktop installed on your system.
- Code editor, such as Visual Studio Code.
# Creating the application

Let's start by creating the application and the project directories. 

1. Create a directory named `node-dev` and inside it a subdirectory named `apps`. ``
2. Inside `~/node-dev/apps` create a file named  `server.js` with the following content:
```javascript
import express from "express";

const app = express();
const PORT = 80;

app.listen(PORT, () => console.log(`Server is listening to PORT ${PORT}`));

app.get("/", (req, res) => {
  res.send("Test server is running!");
});
```

   This is a simple Express server that we will use to test our development environment.

3. Inside `~/node-dev/apps` create a file named  `package.json` with the following content:
```yaml
{
"name": "node-dev",
    "version": "1.0.0",
    "type": "module",
    "scripts": {
        "dev": "nodemon server.js localhost 80"
    },
    "dependencies": {
        "express": "^4.18.2"
    },
    "devDependencies": {
        "nodemon": "^2.0.22"
    }
}
```
We will launch our server using the `nodemon` tool that will make it restart automatically whenever you change your code.

There is no need to install the dependencies now because we will do it later inside the container.
# Creating a container definition with Docker Compose

In Docker Compose, a container configuration is defined in a file called `docker-compose.yml`.

1. In the `node-dev` directory, create a file named `docker-compose.yml` with the following content:
```yaml
version: '3'
services:  
  dev:
    image: node:20
    volumes:
      - ./apps:/home/app
    working_dir: /home/app
    ports:
      - 3000:80
    command: >
      bash -c "npm i &&
      npm i -g nodemon &&
      nodemon -L server.js localhost 80"
```
Let's examine the structure of `docker-compose.yml`:
- `version`: Version of Docker Compose.
- `services`: List of containers.
   - `dev`: Name of our container.
      - `image`: Image used to start the container.
      - `volumes`: Mounting point of a local directory (`./apps`) into a container directory (`/home/app`). 
      - `working_dir`: Working directory inside the container.
      - `ports`: Mapping of a local port (3000) to a container port (80).
      - `command`: Commands that are executed when the container is launched. In our case, these commands install all dependencies and start the server.

# Running the container

Now that we have the container definition, we can launch it.

1. Make sure that Docker Desktop is running.
2. Open the command line in the `node-dev` directory.
3. Build and run the container by executing the following command:  
```bash
$ docker compose up
``` 

Now, if you open the link `localhost:3000` in your web browser, you will see the message "Test server is running!".

Try changing this text on your local machine and reload the page. After some delay, you will see the new text in the browser.

Now you can develop and test your application inside a container without worrying about conflicting dependencies or inconsistent environment.

You can also easily share your container with other members of your development team.

# Conclusion

In this tutorial, we learned how to create a simple development environment with Docker Compose. 

If you want to use containers to develop bigger projects, you can also use Docker Compose to create several services and configure them in a way that allows them to share resources and interact with each other.
