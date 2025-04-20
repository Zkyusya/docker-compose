# docker-compose
In our last session, we learnt how to create dockerfile, docker images, docker containers and push the docker images to the dockerhub.

Today, we learn how to use docker compose to manage multi-container docker applications by simplifying running interconnected services, such as a frontend, backend API, and database, by allowing them to be launched and controlled together.

We will be using YAML configuration file --typically docker-compose.yml  where you can describe each service and its dependencies as code.

Once defined, all services can be started with a single docker-compose command, making it easier to coordinate development or testing environments.

The YAML file defines all the services that make up your application, along with their configurations. Each service specifies its image, ports, volumes, networks, and any other settings necessary for its functionality.

In this hands-on guide, you'll first see how to build and run a counter web application based on Node.js, an Nginx reverse proxy, and a Redis database.(ps// the code used here is copied from the official docker page for demonstration purpose and you can as well apply the same using your own code).

Step 1: let's create a docker-compose.yml in the root file
Before creating the docker-compose.yml, ensure that you have dockerfiles created in both frontend and backend because compose will build this Dockerfile later to create the Docker image for your application.

![image](https://github.com/user-attachments/assets/d35f0e04-c85f-4f78-a54b-518cc3e52b8b)

add the following code to the docker-compose.yml file

services:
  redis:
    image: redis
    ports:
      - '6379:6379'
  web1:
    restart: on-failure
    build: ./web
    hostname: web1
    ports:
      - '81:5000'
  web2:
    restart: on-failure
    build: ./web
    hostname: web2
    ports:
      - '82:5000'
  nginx:
    build: ./nginx
    ports:
    - '80:80'
    depends_on:
    - web1
    - web2

    Heey dont freak out..lets dive into some explanation of whats going on above....

1)The top-level services field is where you define the containers that your app requires.

2)Two services are specified for this app: app (your web application) and redis (your Redis server).

3)Each service has an image field that defines the Docker image the container will run. In the case of the app service, it’s the custom app:latest image. As this may not exist yet, the build field is set to tell Compose it can build the image using the working directory (.) as the build context. The redis service is simpler, as it only needs to reference the official Redis image on Docker Hub. Exposes Redis port 6379 on the host machine (mapped to port 6379 in the container)

4)The app service has a ports field that declares the port bindings to apply to the container, similarly to the  -p flag of docker run. An interpolated variable is used; this means that the port number given by your APP_PORT environment variable will be supplied when it’s set, with a fallback to the default port 80.

5) The nginx service depends on both web1 and web2 services (they will start first). It is configured as a reverse proxy/load balancer for the two web services.


Step 2: Use the docker compose up command to start the application:

Call docker compose up to start all the services in your docker-compose.yml file. In the same way as when calling docker run, you should add the -d argument to detach your terminal and run the services in the background:

   docker compose up -d --build

   ![image](https://github.com/user-attachments/assets/13e81912-70ed-4a9c-a63c-6d6230e6b59e)

   ![image](https://github.com/user-attachments/assets/ef064474-b4f2-4197-baff-27fbd4152645)

   and yeeeees...visit your localhost in your browser to see your app running

   ![image](https://github.com/user-attachments/assets/3a0b646d-7f80-4db4-b174-3e6a57d85c74)

   You can also use compose profiles, compose projects, compose environment variables etc

   before you go...lemmie give you the CLI docker compose cheatsheet for your future use

   ![image](https://github.com/user-attachments/assets/d65a98ac-4885-4192-8a31-c1af003a168d)

   ![image](https://github.com/user-attachments/assets/c1a997d0-aad6-41b9-9bbe-685df4bf66f9)

   ![image](https://github.com/user-attachments/assets/5839fdbf-cf38-44ec-9e9c-466b9bf5806a)

   ![image](https://github.com/user-attachments/assets/4e67652a-63da-46d5-a40d-04304e10cdf3)

In another section, we will dive more into more docker compose examples using real world scenarios like

1)WordPress (Apache/PHP and MySQL) with Docker Compose
2)Prometheus and Grafana with Docker Compose

But for now, lets check on our next tutorial ---Container orchestration. Docker run and docker compose up will not be enough to manage multiple containers in different hosts. And that takes us to the next topic on using Docker swarm and kubernetes(k8s). we will dive in both and see how they work! see you!





