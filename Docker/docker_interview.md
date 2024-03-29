# Question 1

What is docker volume and its usage?

A Docker volume is a persistent data storage mechanism that allows containers to store and share data with the host system or other containers.
Volumes provide a way to persist data beyondthe lifecycle of a container, ensuring that data is retained even if the container is stopped
or deleted.

# Question 2

Difference between CMD and ENTRYPOINT?

In summary, CMD is typically used to specify the main command and arguments that should be executed when a container starts, 
while ENTRYPOINT is used to define the main executable for the container. They can be used together, 
with ENTRYPOINT providing the executable and CMD providing default arguments, allowing users to override the default arguments provided by CMD.

docker run -d image_name:v1 ping google.com --> In this case whatever CMD mentioned in the docker file will be replaced with the ping google.com.
Whereas if ENTRYPOINT is mentioend inthe docker file then it will be executed irrespective of the command line argument given at the time of
docker run command.

# Question 3

What is multi-stage docker file and its usage?

A multi-stage Dockerfile is a feature that allows you to use multiple `FROM` statements in a single Dockerfile. 
Each `FROM` statement starts a new build stage with a fresh build context, but you can selectively copy artifacts
from previous stages into subsequent stages. This feature is particularly useful for creating smaller and more secure Docker images,
as you can use one stage for building your application and another for packaging it without including unnecessary build dependencies 
or intermediate files.

Here's an example of a multi-stage Dockerfile for a Node.js application:

```Dockerfile
# Stage 1: Build the application
FROM node:14 as builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Create the production image
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

In this example:
1. **Stage 1 (Builder)**: This stage uses the official Node.js Docker image as a base to build the application.
 It copies the package.json and package-lock.json files, installs dependencies, copies the application code,
 and builds the application using npm run build.
3. **Stage 2 (Production)**: This stage starts with a lightweight Nginx image.
   It copies the built application files from the previous stage into the appropriate location within the Nginx image.
   Finally, it exposes port 80 and starts the Nginx server.

Benefits of using multi-stage builds:
- Smaller image size: The final image only includes the necessary artifacts from the builder stage, resulting in a smaller image size.
- Improved security: Unnecessary build dependencies and intermediate files are not included in the final image, reducing the attack surface.
- Simplified build process: All build steps are defined within a single Dockerfile, making the build process easier to manage.

Overall, multi-stage builds are a powerful feature of Docker that can help optimize Docker images for production use cases.

# Question 4

How to reduce the build time of an image. Suppose it is taking 15 mins now.

Use multi-stage build.

# Question 5

Should we use multiple cmd command or single cmd with multple commands using && ?

In a Dockerfile, it's generally better to use a single `CMD` instruction with multiple commands joined by `&&` instead of multiple `CMD` 
instructions. Here's why:

1. **Container Startup**: The `CMD` instruction defines the command to run when the container starts. If you specify multiple `CMD`
2. instructions, only the last one will take effect. Therefore, using a single `CMD` with multiple commands allows you to define
3.  the complete startup command for the container.

4. **Shell Execution**: Each `CMD` instruction runs in its own shell session. Using multiple `CMD` instructions results in
5. starting a new shell for each command, which can be less efficient. On the other hand, when multiple commands are combined using `&&`,
6. they are executed within the same shell session, improving performance.

7. **Error Handling**: When using `CMD` with `&&`, if any command fails (returns a non-zero exit code), subsequent commands will
8. not be executed. This behavior allows for better error handling and ensures that the container does not start if an essential command fails.

9. **Readability and Maintenance**: Combining commands using `&&` in a single `CMD` instruction improves readability
10. and maintainability of the Dockerfile. It keeps all container startup logic in one place, making it easier for developers
11. to understand and modify.

Here's an example illustrating the difference:

Using Multiple `CMD` Instructions:
```dockerfile
CMD echo "Starting Application"
CMD npm install
CMD npm start
```

Using Single `CMD` with Multiple Commands:
```dockerfile
CMD echo "Starting Application" && npm install && npm start
```

In summary, while both approaches are technically valid, using a single `CMD` instruction with multiple commands joined by `&&`
is preferred for better control, efficiency, and maintainability of Dockerfiles.

# Question 6

Explain all the components of dockerfile.

A Dockerfile consists of a series of instructions that define how to build a Docker image. Each instruction serves a specific purpose in the image-building process. Here's an explanation of the common components of a Dockerfile:

1. **FROM**: Specifies the base image upon which the new image will be built. It is typically the first instruction in a Dockerfile.

2. **MAINTAINER**: Optional. Specifies the name and email address of the image maintainer.

3. **LABEL**: Optional. Adds metadata to the image in key-value pairs.

4. **RUN**: Executes commands in the shell during the image build process. These commands are run in a new layer on top of the current image.

5. **COPY**: Copies files and directories from the host machine to the image filesystem.

6. **ADD**: Similar to COPY, but with additional features like unpacking compressed files and downloading files from URLs.

7. **WORKDIR**: Sets the working directory for subsequent instructions in the Dockerfile.

8. **ENV**: Sets environment variables in the container.

9. **USER**: Sets the user or UID to use when running the container.

10. **EXPOSE**: Informs Docker that the container listens on specific network ports at runtime.

11. **CMD**: Specifies the default command to execute when the container starts. It can be overridden by providing arguments during container runtime.

12. **ENTRYPOINT**: Specifies the command to run when the container starts. Unlike CMD, it cannot be overridden by providing arguments during container runtime.

13. **VOLUME**: Creates a mount point with the specified name and marks it as externally mountable.

14. **ARG**: Defines variables that users can pass at build-time to customize the build process.

These components, used together in a Dockerfile, allow users to define the steps needed to build an image and encapsulate an application along with its dependencies.

# Question 7

How can you pass environment variables to a Docker container?

You can pass environment variables to a Docker container in several ways:

1. **Using the `-e` or `--env` option**:
   You can use the `-e` or `--env` option when running a container to set one or more environment variables. You can specify the variable name and value directly on the command line.

   ```bash
   docker run -e VARIABLE_NAME=value image_name
   ```

   For example:
   ```bash
   docker run -e MYSQL_ROOT_PASSWORD=my-secret-password mysql
   ```

2. **Using a file with environment variables**:
   You can create a file containing environment variables in key-value pairs and pass it to the container using the `--env-file` option.

   ```bash
   docker run --env-file=path/to/env-file image_name
   ```

   For example, if you have a file named `env.list` containing:
   ```
   VARIABLE_NAME=value
   ```

   You can run the container using:
   ```bash
   docker run --env-file=env.list image_name
   ```

3. **Setting environment variables in Dockerfile**:
   You can set environment variables directly in the Dockerfile using the `ENV` instruction. This will set the environment variables in the container when it starts.

   ```Dockerfile
   FROM image_name
   ENV VARIABLE_NAME=value
   ```

   For example:
   ```Dockerfile
   FROM nginx
   ENV MY_VAR hello
   ```

4. **Using Docker Compose**:
   If you're using Docker Compose to manage your containers, you can specify environment variables in the `docker-compose.yml` file under the `environment` section.

   ```yaml
   services:
     my_service:
       image: image_name
       environment:
         VARIABLE_NAME: value
   ```

   For example:
   ```yaml
   services:
     my_service:
       image: nginx
       environment:
         MY_VAR: hello
   ```

These are some common methods for passing environment variables to Docker containers. Choose the method that best suits your use case and workflow.

# Question 8

How to remove unused resources in docker?

To clean up unused Docker resources, including stopped containers, dangling images, unused networks, and unused volumes, you can use various Docker CLI commands. Here are the commands you can use:

1. **Remove Stopped Containers**:
   Use the `docker container prune` command to remove all stopped containers.

   ```bash
   docker container prune
   ```

2. **Remove Dangling Images**:
   Dangling images are images that have no associated containers. You can remove them using the `docker image prune` command.

   ```bash
   docker image prune
   ```

3. **Remove Unused Volumes**:
   Use the `docker volume prune` command to remove unused volumes.

   ```bash
   docker volume prune
   ```

4. **Remove Unused Networks**:
   Unused networks are networks that are not connected to any container. You can remove them using the `docker network prune` command.

   ```bash
   docker network prune
   ```

5. **Remove All Unused Resources**:
   To remove all unused resources at once, you can use the `docker system prune` command with the `-a` or `--all` option.

   ```bash
   docker system prune -a
   ```

   This command removes all stopped containers, dangling images, unused networks, and unused volumes.

Before running these commands, it's important to review the resources that will be deleted to ensure that you don't accidentally remove any important data. Additionally, be aware that these commands permanently delete the specified resources, and the operation cannot be undone.
