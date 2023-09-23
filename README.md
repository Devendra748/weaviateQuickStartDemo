# Connecting Weaviate Locally with Docker

Weaviate is a powerful vector database that enables semantic searches and generative queries. To get started with Weaviate locally, you can use Docker to quickly set up and run a local instance. This document will guide you through the process of connecting Weaviate locally using Docker.

## Prerequisites

Before you begin, make sure you have the following:

- Docker installed on your local machine. You can download and install Docker from the official website: [Docker Download](https://www.docker.com/products/docker-desktop).

## Step 1: Pull Weaviate Docker Image

1. Open your terminal or command prompt.

2. Use the following command to pull the Weaviate Docker image from the official Docker Hub repository:

   ```bash
   docker pull semitechnologies/weaviate
   ```

   This command will download the latest Weaviate image to your local machine.

## Step 2: Run Weaviate Container

1. After the image has been successfully pulled, you can run a Weaviate container using the following command:

   ```bash
   docker run -d -p 8080:8080 --name weaviate semitechnologies/weaviate
   ```

   - `-d`: Runs the container in detached mode (in the background).
   - `-p 8080:8080`: Maps port 8080 from the container to your local machine.
   - `--name weaviate`: Assigns the name "weaviate" to the container.

   This command starts the Weaviate container locally.

2. To verify that the container is running, you can check the running containers using the following command:

   ```bash
   docker ps
   ```

   You should see the "weaviate" container listed with its status as "Up."

## Step 3: Access Weaviate

1. Weaviate should now be accessible at `http://localhost:8080` in your web browser.

2. You can interact with Weaviate using the Weaviate RESTful API. For example, you can make API requests to create classes, add objects, and perform searches.

3. To stop the Weaviate container when you're done, use the following command:

   ```bash
   docker stop weaviate
   ```

## Conclusion

You have successfully connected Weaviate locally using Docker. You can now use Weaviate for your vector-based applications, semantic searches, and generative queries. Remember to stop the Docker container when you're finished using Weaviate locally to free up system resources.
