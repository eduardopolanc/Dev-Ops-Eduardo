1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?

Because it is more secure to assing environmental variables that involve passwords when we create the repository than in files with text. if those files are opened they can expose the passwords. This way sensitive information is not in the source code of the container.

1-2: Why do we need a volume to be attached to our postgres container?
Because we need to save the content of the container somewhere, otherwise it will disappear when we destroy the container. it is like isolating backup space inside the host hardware.

1-3: Document your database container essentials: commands and Dockerfile.
Content of Dockerfile:
FROM postgres:17.2-alpine

ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd

COPY docker-entrypoint-initdb.d /docker-entrypoint-initdb.d

Commands used:
docker build -t database . #Build the image

docker network create app-network #Create the network

docker run -d \ #Creating the container with the volume
  --name postgres-db \
  --network app-network \
  -v C:\Users\user\Desktop\EPF\Dev_Ops\Dev-Ops-Eduardo\TP\docker-data:/var/lib/postgresql/data \
  database

docker run -p "8080:8080" --net app-network --name adminer -d adminer #running adminer for visualization

1-4 Why do we need a multistage build? And explain each step of this dockerfile.

It is important because it helps to compress the size of the image by running temporary processes and then extracting just the needed output for another process to run. Then, the remining parts that are not needed anymore are removed.

The code to create the multistate image works as follow:

#Build stage: Create an image based on eclipse-temurin:21-jdk-alpine to compile the app into .jar files needed for future processes with maven.

# Run stage: Copy the .jar files generated and run then with ENTRYPOINT.

at the end, docker just keep the last image called with FROM and delete all the others, in this case, deletes myapp-build.


