+++
title = "Docker Basics"
date = 2019-10-28T11:40:22+11:00
weight = 1
+++

## Docker Basics
To get you warmed up today, we're going to focus on the fundamentals of Docker and how to use it locally within one machine. Or in this case, within your [**AWS Cloud9**](/setup/launchcloud9.html) workspace.

Let's start by running `docker version` to confirm that both the client and server are there and working.

        docker version

Docker containers are built using images. Let's run the command `docker pull nginx:latest` to pull down the latest nginx trusted image from Docker Hub.

        docker pull nginx:latest

Now run `docker images` to verify that the image is now on your local machine's Docker cache. If we start it then it won't have to pull it down from Docker Hub first.

        docker images

Now let's try `docker run -d -p 8080:80 --name nginx nginx:latest` to instantiate the nginx image as a background daemon with port 8080 on the host forwarding through to port 80 within the container

        docker run -d -p 8080:80 --name nginx nginx:latest

Run `docker ps` to see that our nginx container is running.

        docker ps

Try `curl http://localhost:8080` to use the nginx container and verify it is working with its default `index.html`.

        curl http://localhost:8080

Running `docker logs nginx` shows us the logs produced by nginx and the container. You should see some events that correspond to our curl requests.

        docker logs nginx

Use `docker exec -it nginx /bin/bash` to start an interactive shell into the container's filesystem and constraints

        docker exec -it nginx /bin/bash

From within the container, run `cd /usr/share/nginx/html` and `cat index.html` to see the content the nginx is serving which is part of the container.

        cd /usr/share/nginx/html
        cat index.html

Type `exit` to exit our shell within the container.

        exit

Now run `docker stop nginx` to stop the container.

        docker stop nginx

Try `docker ps -a` command and you should see that our container is still there but stopped. At this point it could be restarted with a `docker start nginx` if we wanted.

        docker ps -a

To remove the container once and for all, use the `docker rm nginx` command.

        docker rm nginx

Now you can use `docker rmi nginx:latest` to remove the nginx image from our machine's local cache

        docker rmi nginx:latest

It's now time for some Git-Fu. Pull down this repo onto your workspace with `git clone https://github.com/jasonumiker/docker-kube-intro-workshop.git`

        git clone https://github.com/jasonumiker/docker-kube-intro-workshop.git

Type `cd docker-kube-intro-workshop` to change into that project.

        cd docker-kube-intro-workshop

Run `cat Dockerfile` to see our simple `Dockerfile` - this is just adding the local `index.html` to the container image overwriting the default.

        cat Dockerfile

Use `docker build -t nginx:1.0 .` to build nginx from our Dockerfile

        docker build -t nginx:1.0 .

You can now use `docker history nginx:1.0` to see all the steps and base containers that our nginx:1.0 is built on. **Note that our change amounts to one new tiny layer on top.**

        docker history nginx:1.0

Type `docker run -p 8080:80 --name nginx nginx:1.0` to run our new container. Note that we didn't specify the `-d` to make it a daemon which means it holds control of our terminal and outputs the containers logs to there which can be handy in debugging.

        docker run -p 8080:80 --name nginx nginx:1.0

Open another Terminal tab (**Window -> New Terminal**)

Run `curl http://localhost:8080` in the other tab a few times and see our new content.

        curl http://localhost:8080

Go back to the first tab and see the log lines sent right out to STDOUT.

At this point we could push it to Docker Hub or a private Rr    egistry like [Amazon ECR](https://aws.amazon.com/ecr/) for others to pull and run. **We won't worry about that yet though.**

Type **Ctrl-C** to exit the log output. Note that the container has been stopped but is still there by running a `docker ps`.

        docker ps -a

Use `sudo docker inspect nginx` to see lots of info about our stopped container.

        sudo docker inspect nginx

As we did earlier, use `docker rm nginx` to delete our container.

        docker rm nginx

For our last magic trick, we're going to try mouting some files from the host into the container rather than embedding them in the image. Run `docker run -d -p 8080:80 -v /home/ec2-user/environment/docker-kube-intro-workshop/index.html:/usr/share/nginx/html/index.html:ro --name nginx nginx:latest`

        docker run -d -p 8080:80 -v /home/ec2-user/environment/docker-kube-intro-workshop/index.html:/usr/share/nginx/html/index.html:ro --name nginx nginx:latest

Now try a `curl http://localhost:8080`. Note that even though this is the upstream nginx image from Docker Hub **our content** is there.

        curl http://localhost:8080

Edit the **index.html** file and add some more things.

Restart the container. It'll remember all the command-line options we ran it with when starting it again.

        docker stop nginx && docker start nginx

Try another `curl http://localhost:8080` and note the immediate changes.

        curl http://localhost:8080

Finally, run `docker stop nginx` and `docker rm nginx` to stop and remove our last container.

        docker stop nginx && docker rm nginx
