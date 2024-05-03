# Setting up Bookstack with Docker on macOS
How to setup a running Bookstack instance in a Docker container on MacOS using a compose file

## Why make this?
I ran into a few issues learning Docker and it wasn't as straightforward on Mac as it was for other implementations.

Also, updates to Docker caused video tutorials to be of little help in this process. This is to help anyone that finds this guide as it 
explains why certain things will be done the way they are instead of just blindly following along.

This is meant for people who are trying to learn a bit about Docker using compose files. I myself learn more through teaching a concept
so it has the added benefit of solidifying the information for myself.

## What is Docker?
Docker is essentially a software package that wraps around your application and **ALL** of its dependencies. For example, say your application needs
a database and various other packages. Then all of that will be wrapped in the Docker container so that any system anywhere can have everything it needs
to run your application. 

## Installing Bookstack with a compose file
I followed the instructions from [Bookstack's site](https://www.bookstackapp.com/docs/admin/installation/)

I initially tried to install manually but kept running into issues. So I went with the compose file route. Scroll down to the header: **Docker Containers**

Here there is a link to [LinuxServer.io repository](https://github.com/linuxserver/docker-bookstack)

Read through the README file and you'll come accross a compose file. The file itself it a bit outdated and video tutorials don't account for this either.

As of now "docker-compose" is depreciated and no longer used with your terminal/command line. The word docker is now the active application in your PATH and it 
has the 'compose' command associated with it. Also the use of 'version' inside the .yml file is now depreciated and isn't required. The naming convention for the 
docker compose file is now just 'compose.yml' instead of 'docker-compose.yml'

The other issue I ran into was the APP_URL. On mac, you need to use your full localhost address **__AND__** the port you have assigned to the connect to the container. 
Every Docker container has a port and you need to assign an unused port to that Docker port on your machine so it will let you through. This port you've assinged
has to be attached to the localhost address.

The ports work as such: 'your assigned port':'Docker port' so you change the first port to an unassigned port for your system. In the example below the computer port is
changed to 6875. Making the ports section look like: - 6875:80

The APP_URL for mac should look like this: '--https://localhost:6875--'

To start create and navigate to your application folder directory and create your compose file. It is a yaml file so 'compose.yml' is what Docker will look for.

The updated compose file should look like:

```python
services:
  bookstack:
    image: lscr.io/linuxserver/bookstack
    container_name: bookstack
    environment:
      - PUID=1000
      - PGID=1000
      - APP_URL=https://localhost:6875
      - DB_HOST=bookstack_db
      - DB_PORT=3306
      - DB_USER=bookstack
      - DB_PASS=<yourdbpass>
      - DB_DATABASE=bookstackapp
    volumes:
      - ./bookstack_app_data:/config
    ports:
      - 6875:80
    restart: unless-stopped
    depends_on:
      - bookstack_db
  bookstack_db:
    image: lscr.io/linuxserver/mariadb
    container_name: bookstack_db
    environment:
      - PUID=1000
      - PGID=1000
      - MYSQL_ROOT_PASSWORD=<yourdbpass>
      - TZ=Europe/London
      - MYSQL_DATABASE=bookstackapp
      - MYSQL_USER=bookstack
      - MYSQL_PASSWORD=<yourdbpass>
    volumes:
      - ./bookstack_db_data:/config
    restart: unless-stopped
```
