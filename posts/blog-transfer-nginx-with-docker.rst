.. title: Blog Transfer Nginx with Docker
.. slug: blog-transfer-nginx-with-docker
.. date: 2017-07-24 16:19:50 UTC
.. tags: docker, nginx, blog 
.. category: tech
.. link: 
.. description: 
.. type: text

I'm very impressed with Docker containerization, and while studying it, I just wanted to try some of the basic things I've learned. If you're not familiar with Containerization and Docker, let me explain it in simple words.

Container
  Is an isolated set of packages including libraries, applications that are completely independent of its surroundings.

Docker
  Docker is a container, which packages up an application and all its dependencies.

My plan is to use **Nginx** as the webserver and run it in a Docker container. Then I'll generate my site from Nikola and deploy it in the Nginx server.

1. Install Docker CE

   how-to-install-docker: https://docs.docker.com/engine/installation/linux/docker-ce/debian/#install-using-the-repository

2. Download Nginx image

   .. code-block:: Bash

      sudo docker pull nginx:latest

3. Port forwarding

   Now, before I initialize and run the container, I need to decide which port I'm going to use to redirect from my host server to the container. Here I'm using port 80->80 forwarding.

4. Directory mapping

   The deployment directory of Nginx is, /usr/share/nginx/html. This directory needs to be mapped with a host server directory.

   Directory map
     /home/user/www/output -> /usr/share/nginx/html

5. Create and run the container

   .. code-block:: Bash

      sudo docker run -d -p 80:80 --name=WebServer1 -v /home/user/www/output:/usr/share/nginx/html nginx:latest

   p
    Option is used to forward the ports.

   name
    Is the container name.

   v
    Direcotry mapping with host and container.

     
This will create a container called **WebServer1** based on Nginx image and deploy the files in `/home/user/www/output/` directory.
