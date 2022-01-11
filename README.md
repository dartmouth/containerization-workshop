# Docker RC Workshop

## Agenda

* Containerization Overview
  * What is a container? (VM=house, Container=apartment)
  * Why should you use containers?
    * Speed
    * Reproducibility
  * Docker vs. Containerization (Kleenex vs. Tissue)
  * Future: Rancher Desktop
* Running your first container
    ```shell
    docker run docker/whalesay:latest cowsay Hello  # Run docker/whalesay container
    docker ps                                       # Display running containers
    docker ps -a                                    # Display all containers
    docker rm -f $(docker ps -a -q)                 # WARNING: Delete all containers
    docker run --rm docker/whalesay cowsay Hello    # Delete container after running 
    docker run --rm -it ubuntu bash                 # Run an interactive ubuntu container
    ```
* Exposing Ports, Running in Background, and Accessing logs
    ```shell
    docker run --name mynginx -d -p 80:80 --restart unless-stopped nginx:alpine
    docker logs -f mynginx
    docker ps
    docker rm -f mynginx
    ```
* Mounting storage
    ```shell
    docker run --rm -p 80:80 -v "$PWD/html":/usr/local/apache2/htdocs httpd:alpine
    ```
* Passing environment variables
    ```shell
    docker run --rm -p 80:80 -v "$PWD/html":/var/www/html -e MYVAR=hello php:7-apache
    ```
* Finding container images: [Docker Hub](https://hub.docker.com)
    ```shell
    docker image ls                        # List local images
    docker image rm httpd:alpine           # Remove local image
    docker image rm $(docker image ls -q)  # Remove all local images
    ```
* Building docker images
    ```shell
    docker build -t mynginx:v1.0.0 .
    docker image ls
    docker run --rm -it -p 80:80 mynginx:v1.0.0
    ```
* Archiving/loading images
    ```shell
    docker save mynginx:v1.0.0 | gzip -9 > ~/mynginx-v1.0.0.tar.gz
    docker image rm mynginx:v1.0.0
    docker image ls
    gunzip -c ~/mynginx-v1.0.0.tar.gz | docker load
    docker image ls
    ```
* Networks
    ```shell
    docker network create todo-app
    docker network ls
    ```
* Multiple containers
    ```shell
    docker run -d \
      --name todo-mysql \
      --network todo-app \
      -v "$PWD/todo-mysql-data":/var/lib/mysql \
      -e MYSQL_ROOT_PASSWORD=secret \
      -e MYSQL_DATABASE=todos \
      mysql:5.7
    docker exec -it todo-mysql mysql -p
      SHOW DATABASES;
    docker run --rm -it --network todo-app alpine
      apk update
      apk add bind-tools
      dig todo-mysql
    docker run -d \
      --name todo-app \
      -p 3000:3000 \
      -w /app -v "$PWD/app":/app \
      --network todo-app \
      -e MYSQL_HOST=todo-mysql \
      -e MYSQL_USER=root \
      -e MYSQL_PASSWORD=secret \
      -e MYSQL_DB=todos \
      node:12-alpine \
      sh -c "yarn install && yarn run dev"
    docker exec -it todo-mysql mysql -p
    docker rm -f todo-app todo-mysql
    docker network rm todo-app
    ```
* Docker compose
    ```shell
    docker-compose up
    docker ps -a
    docker-compose down
    docker-compose up -d
    docker-compose ps
    docker-compose logs -f
    docker-compose down
    ```
* Research containers
    ```shell
    docker run -it --rm -v $(realpath ~/notebooks):/tf/notebooks -p 8888:8888 tensorflow/tensorflow:latest-jupyter
    docker run -it --rm -v $(realpath ~/rstudio):/home/rstudio/rstudio -p 8787:8787 -e DISABLE_AUTH=true rocker/rstudio
    ```
* What does the future hold?
* Questions?
