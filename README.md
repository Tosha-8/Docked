# Docked
1> Orientation:

i- installation:
  a)-either configure the repo with  yum-config-manager --add-repo httpd://...
  yum install docker-ce
  b)-get the rpm package and install.
ii- user and group should be docker
iii- enable daemon 'sudo dockerd &'
4-
-----

2> Containers:

Hierarchy of APP is :
  Stack (top level of the app)
  Services (defines how containers behave)
  Containers (main soace for the app)
#The new dev env: Earlier if developemnet needs to be started it needed a standard env as IDE and runtime installed on the machine
and it needed to be done on all the servers to match with prod.
with Docker you just get dependencies and runtime env as the portable base image(as per the requirement) and build your app on top of it.

Main Components:
Dockerfile: Defines what goes inside your container viz network, services, ports, storage etc (isolated).
It also refers to some other files as requiremets.txt and app.py  under the same folder as Dockerfile.

Once Dockerfile, requiremets and app is added we just have to build the app.

#building the image:
docker build -t <name_your_image> .

#listing all images
docker image ls

#Run the app:
docker run -p 4000:80 <your_image> #-p is used to mapo port 4000 of your machine with port 80 of the container.
#run in detach mode:
docker run -d -p 4000:80 <your_image>

#list the running containers:
docker container ls

#stopping the container
docker container stop <container_id>

#share you image upload it on docker hub.
> login to your repo > docker login from CLI
> tag your image  #docker tag image username/repository:tag
> docker image ls will show remote images also.

#publish the image > docker push username/repo:tag

once done this image can be pulled and run anywhere
docker run -p 4000:80 username/repository:tag

3> Services: In distributed app env, different pieces of app are called services. (this is how we load balance app in docker)
EX: a video sharing site (you tube) which used different services as DB, UI, transcode etc.

pre-requisites{
Intro to docker compose> A tool for defining and running multi-container apps. We can use yaml file to configure the app services.
features of compose: dive deep yourself.
                    #isolating the envs on single hosts (use of project names to isolate envs)
                    #preserving volume data when containers are created.
                    #only recreate containers that have changed.
                    #variable and moving composition b/w containers
to get started we need to install compose (figure out).

Once done we need to make it executable at /usr/local/bin/docker-compose

Using compose:
  1- Define the app env in Dockerfile.
  2- Define the services required for app in docker-compose.yml so they can run together as isolated env.
  3- Run `docker-compose up` to run the entire app.
}

> one service runs one containers specific to that service only.

#Compose file is written on yaml and saved as 'docker-compose.yml' it is needed to create a scalable app run as a service.
once docker-compose filer is created we need to initialize the swarm and deploy the stack as:
#docker swarm init
#docker stack deploy -c docker-compose.yml <name you want to give your app>

# to check the running services > docker service ls
# to scale the app, just increase the value of replicas in compose file and deploy the stack again.

# to take down the app > docker stack rm <name of your app>
# take down the swarm > docker swarm leave --force

4> Swarms
Swarm is a group of machine that are running docker and joined into a cluster. The docker commands that we run
now are executed on a cluster by swarm manager. Machines in swarm can be physical or vms and together they are
called as nodes.
One of the strategy for working of swarm is called 'emptiest node' where swarm manager fills the least utilized
machine with containers.
Global technique where each machine gets exactly one instance of the specified container.
We can manage these techniques in compose file.

## Setting up the Swarm.
> 'docker swarm init' will make the current machine as swarm manager, then run 'docker swarm join' on other machines
to have them join as workers.
> run 'docker swarm join-token` on the worker node this will generate a token which will route the worker machine
to connect wit the manager machine.
once all our worker nodes are connected with manager then we can deplloy the app, using:
# docker stack deploy -c docker-compose.yml <name of the app>


5> Stacks
  it is the top at hierarchy of distributed app.
  Mainly a stack is a group of interrelated services which is used for the implementation of multi service(micro-arch) app.

Now, that we have created a new compose file, which contains a new thing which is peer service to 'web', named 'visualizer'.
The 'volume'key gives access to hosts socket file for docker and a placement key, ensuring that this service runs only on swarm manager.

after we have the new compose file setup, we can re-deploy our app with :
'docker stack deploy -c docker-compose.yml getstartedlab'

> Visualizer is a standalone service that can run in any app that includes it in the stack. It doesnt depends on anything else.

Now we'll add redis DB for storing app data for which we'll add new compose file which will contain image of redis in the block:
redis
  image: redis
which will be pulled from docker library.
>Creation of the directory data is necessory as it will be used to store redis data.
> redis will always run on the manager machine and will use the same filesystem.



##SSH into container##
>docker exec -itr <container id> /bin/bash

Enjoy! Happy learning.
