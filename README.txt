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

1-5 Why do we need a reverse proxy?
We need it for two main things:
	1. security: with a reverse proxy the user cannot access some parts of the application directly. In this case, the backend.
	2. Convenience: it is easier to the user to access all the parts we are wishing to show from one single port than to different ports. this way the user connects to the proxy and the proxy redirect all the trafic.

1-6 Why is docker-compose so important?
Because it help us to create multiple images at the same time dispite Dockerfile which can handle just one image creation. Docker-compose execute multiple dockerfiles.

1-7 Document docker-compose most important commands.

docker-compose up -d

1-8 Document your docker-compose file.

KEY COMMANDS:

  backend:  # Creates the backend image
    build:
      context: ./backend_api/simpleapi  # Location of the Dockerfile
    networks:
      - app-network  # Configuring the network to be used
    depends_on:
      - database  # Building dependency with database container

  database:  # Creating database image
    build:
      context: ./database  # Location of the Dockerfile
    networks:
      - app-network  # Configuring the network to be used
    volumes:
      - pgdata:/var/lib/postgresql/data  # Configuring volume to store DB data.

  httpd:  # Create http image
    build:
      context: ./http-server  # Location of the Dockerfile
    ports:
      - "8082:80"  # configuring port to be used
    networks:
      - app-network  # Configuring the network to be used
    depends_on:
      - backend  # Building depedency

networks:
  app-network:  # Defining network

volumes:
  pgdata:  # Defining volume

1-9 Document your publication commands and published images in dockerhub.
docker tag tp-database eduardo9703/tp-database:1.0
docker tag tp-backend eduardo9703/tp-backend:1.0
docker tag tp-httpd eduardo9703/tp-httpd:1.0

docker push eduardo9703/tp-httpd:1.0
docker push eduardo9703/tp-backend:1.0
docker push eduardo9703/tp-database:1.0

1-10 Why do we put our images into an online repo?
because this way we can share our work to other people to make the programming community bigger and better since we are adding more resources to it. Plus, we can have a secure spot where to save our work and reuse it in the future.

test
