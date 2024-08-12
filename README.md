# docker-challenge-template

Assignment: Containerization with Docker
Some resources

Challenges
Challenge 1 - Simple static page server
Steps
• Use the folder challenge1.
• Setup Docker Environment : Check the docker version in your device by running the – docker --version command in the command prompt.

• Run the docker desktop application.
• Navigate to the directory of Challenge 1 Folder in the VS code.
• Create a "public" folder in this directory under challenge 1.
• Create an index.html file under this folder.
• Create a static page by using !element.
• In the body write your name and student ID in h1 and p tags respectively.
• Create another file in the challenge 1 directory and name it as dockerfile.
• Write the following code in that file : FROM nginx:latest #This will create an image of nginx distribution
COPY public /usr/share/nginx/html #This will the public folder to the shared nginx folder on a particular port
EXPOSE 80 #This will expose port 80 to the html requests for the localhost
CMD [“nginx”, “-g”, “daemon off;”] # With the daemon configuration turned off, this command will launch the default nginx container and execute nginx in the foreground.

• Now run this command in the terminal of the VS code : docker build -t solution-01 .
• This will build the image in the docker.

• To start this image run the following command:
docker run -p 8080:80 -d –name docker-container solution-01
This command launches a new container from the solution-01 image.
-p 8080:80: Binds port 8080 on the host to port 80 within the container.
-d: Operates the container in detached mode, preventing the terminal from being overwhelmed with logs.
--name docker-container: Assigns the name docker-container to the running container.
• The result will be shown as a string.

• Open any browser in the host machine and run localhost:8080, the output will be shown as an html page output oon pport 8080.

This output completes our challenge 1.

Challenge 2 - NodeJS application

• Setup the environment as we did in the challenge1.
• Extract the files in the directory named Challenge2.
• Open the extracted directory in Visual Studio Code to begin the setup.
• In the project directory, create a file named Dockerfile with the following content:
FROM node:latest
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]

• This Dockerfile performs the following actions:
Pulls the latest Node.js image.
Sets the working directory to /app.
Copies package.json into the working directory.
Installs the necessary dependencies.
Copies the rest of the application files.
Exposes port 3000.
Starts the application with npm start.
• In the same directory, create a file named docker-compose.yml with the following content:

This file defines the services for Docker Compose:
api: Builds the image from the current directory, maps port 3000, and sets the environment variable.
nginx: Uses the latest NGINX image, maps port 80 in the container to port 8080 on the host, and mounts the nginx.conf file. It depends on the api service.

• In the project directory, create a file named nginx.conf with the following configuration:

This configuration file sets up NGINX to act as a reverse proxy, forwarding requests from http://localhost:8080/api to the Node.js API running on http://api:3000.
• Start the Docker services by running the following command in the terminal:
docker-compose up

• This command builds the images and starts the containers as defined in the docker-compose.yml file.
• Open a web browser and navigate to the following URLs to verify the setup:
http://localhost:8080/api/books: Should display a JSON list of books.

http://localhost:8080/api/books/1: Should display the JSON of a specific book with ID 1.

CHALLENGE 3: DOCKER COMPOSE FULL-STACK APPLICATION

1. Introduction
   This report describes the process of setting up and executing a full-stack application using Docker Compose. The application comprises a MariaDB database, a Node.js API service, and an Nginx server acting as a reverse proxy.
   The goal of Challenge 3 was to dockerize a Node.js application that connects to a MySQL database. This challenge was aimed at understanding how to containerize an application and link it with a database container.
2. Steps Taken
   2.1 Initial Setup
   Create the Node.js Application:
   Start by creating a basic Node.js application. Initialize a new Node.js project using the following command:
   bash
   Copy code
   npm init -y
   Install the necessary dependencies, such as Express.js and MySQL, using:
   bash
   Copy code
   npm install express mysql
   Create a basic server that connects to a MySQL database and exposes a simple API.
   Set Up the MySQL Database:
   Create a docker-compose.yml file to define the MySQL service. For example:
   yaml
   Copy code
   version: '3.7'
   services:
   db:
   image: mysql:5.7
   environment:
   MYSQL_ROOT_PASSWORD: rootpassword
   MYSQL_DATABASE: mydatabase
   MYSQL_USER: user
   MYSQL_PASSWORD: password
   ports: - "3306:3306"
   Run the MySQL container using Docker Compose:
   bash
   Copy code
   docker-compose up -d db
   2.2 Dockerizing the Node.js Application
   Create a Dockerfile:
   Inside your Node.js application directory, create a Dockerfile with the following content:
   dockerfile
   Copy code
   FROM node:14
   WORKDIR /app
   COPY package\*.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["node", "index.js"]
   This Dockerfile does the following:
   Uses the Node.js 14 image as the base.
   Sets the working directory inside the container to /app.
   Copies the package files and installs dependencies.
   Copies the rest of the application files.
   Exposes port 3000, where the application will run.
   Defines the command to start the Node.js application.
   Build and Run the Docker Image:
   Build the Docker image for your application:
   bash
   docker build -t node-service .

Run the container, linking it to the MySQL container:
docker run -d -p 3000:3000 --name node-app --link db:mysql node-service
2.3 Testing the Application
Test the API:
Make a GET request to the Node.js application to ensure it correctly retrieves data from the MySQL database.
Example:
• All Books: http://localhost:8080/api/books
• Single Book by ID: http://localhost:8080/api/books/1
Verify that the API response contains data fetched from the database. 3. Conclusion
Dockerizing the Node.js application and linking it with a MySQL database container helped in understanding the containerization process. This setup ensures that the application and its dependencies are packaged together, making it easier to deploy and manage in different environments.

CHALLENGE 4: SCALING UP AN APPLICATION

1. Introduction
   Building on the application from Challenge 3, the objective of Challenge 4 was to use Docker Compose to scale the Node.js service from one to three instances. This exercise highlights the importance of load balancing, fault tolerance, and improved performance through horizontal scaling.
2. Steps Taken
3. Base Setup:
   o The application setup from Challenge 3 was used as the base for Challenge 4.
   o Ensured that the application was running correctly with a single instance of the Node.js service.
   2.2 Testing the Single Instance
4. Test with Single Instance:
   o Made a GET request to http://localhost:8080/api/stats using a browser or REST client.
   o Recorded the hostname in the response. With only one instance running, the hostname remained consistent across multiple requests.
   2.3 Scaling the Node.js Service
5. Modify docker-compose.yml:
   o Adjust the docker-compose.yml file to include a deploy section to scale the service:
   yaml
   Copy code
   services:
   api:
   image: your-username/node-service:latest
   environment:
   DB_HOST: ${DB_HOST}
   DB_USERNAME: ${DB_USERNAME}
   DB_PASSWORD: ${DB_PASSWORD}
   DB_DATABASE: ${DB_DATABASE}
   ports: - "3000:3000"
   deploy:
   replicas: 3 # Scale the Node.js service to 3 instances
   depends_on: - db
   o This configuration ensures that Docker Compose will create three instances of the api service.

6. Scale the Service:
   o Run the following command to scale the service:
   bash
   Copy code
   docker-compose up -d --scale api=3
   o Verify that three instances of the api service are running using:
   bash
   Copy code
   docker-compose ps
   2.4 Post-Scaling Testing
7. Test Load Balancing:
   o After scaling, multiple GET requests were made to http://localhost:8080/api/stats.
   o Observed that the hostname value changed with each request, indicating that the load was being distributed among the three instances of the api service.
8. Expected Outcomes
   • Before Scaling: The hostname remained the same across multiple requests.
   • After Scaling: The hostname varied with each request, confirming that Docker Compose correctly distributed the load among the three instances.

9. Conclusion
   Docker Compose's ability to handle load balancing and service replication was effectively proven by scaling the Node.js service to three instances. The program improved in handling increasing traffic and became more resilient to service outages by spreading the burden over numerous instances. The significance of horizontal scaling in contemporary, containerized environments was highlighted by this exercise.

With this all our 4 challenges have been completed.
