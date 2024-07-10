# JWT Pizza Service Container

🔑 **Key points**

- Build a container for the JWT Pizza Service.

---

Now that you know how Docker containers work, you need to create a jwt-pizza-service Docker container image from the Pizza Service source code. Here are the steps to take.

1. In your development environment, open your command console and navigate to the directory containing your fork of `jwt-pizza-service`.
1. Create a file named `Dockerfile` in the project directory with the following content.

   ```dockerfile
   ARG NODE_VERSION=20.12.2

   FROM node:${NODE_VERSION}-alpine
   WORKDIR /usr/src/app
   COPY . .
   RUN npm ci
   EXPOSE 80
   CMD ["node", "index.js", "80"]
   ```

1. Modify/Create the `config.js` file. Set the database host field so that it looks outside of the container for the MySQL server by specifying the value of `host.docker.internal`. Make sure you include the `config.js` file in your `.gitignore` file so that you do not accidentally push it to your repository. Set the parameters, such as the jwtSecret and factory.apiKey, according to your environment.
   ```sh
   module.exports = {
    jwtSecret: 'yourRandomJWTGenerationSecretForAuth',
    db: {
      connection: {
        host: 'host.docker.internal',
        user: 'root',
        password: 'yourDatabasePassword',
        database: 'pizza',
        connectTimeout: 60000,
      },
      listPerPage: 10,
    },
    factory: {
      url: 'https://pizza-factory.cs329.click',
      apiKey: 'yourHeadquartersProvidedApiKey',
    },
   };
   ```
1. Copy the source code files to `dist` that we want to distribute.
   ```sh
   mkdir dist
   cp Dockerfile dist
   cp -r src/* dist
   cp *.json dist
   ```
1. Navigate to the `dist` directory.
   ```sh
   cd dist
   ```
1. Build the image.
   ```sh
   docker build -t jwt-pizza-service .
   ```
1. Verify that the container exists.

   ```sh
   ➜  docker images -a

   REPOSITORY         TAG      IMAGE ID       CREATED         SIZE
   jwt-pizza-service  latest   9689e2852c3a   2 seconds ago   132MB
   ```

1. Run the container and make sure it works. Substitute the image ID with the correct value for your newly created Docker image.

   ```sh
   docker run -d --name jwt-pizza-service -p 80:80 9689e2852c3a

   curl localhost:80
   ```

   This should return the service welcome response if the container is successfully running.

1. Stop and delete the container
   ```sh
   docker rm -fv jwt-oizza-service
   ```

## ☑ Assignment

Create a JWT Pizza Service container using the instructions given above. This includes the following steps:

1. Create the Dockerfile.
1. Create a distribution directory.
1. Modify your `config.json` file so that the container can access your external database.
1. Build the container image.
1. Run the container.
1. Shutdown the container.

Once you are done, go over to Canvas and submit a screenshot of the console window showing the build, list, and run commands. This should look something like the following.

![Docker successful run](dockerSuccess.png)
