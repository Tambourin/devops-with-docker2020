# devops-with-docker2020
Excercises for the course DevOps with Docker

# Part 1
## Excercise 1.1
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
649fa259b877        nginx               "nginx -g 'daemon of…"   41 seconds ago      Exited (0) 8 seconds ago                       zealous_buck
64f94e763fd3        nginx               "nginx -g 'daemon of…"   42 seconds ago      Exited (0) 4 seconds ago                       adoring_golick
c5efe56085da        nginx               "nginx -g 'daemon of…"   43 seconds ago      Up 43 seconds              80/tcp              gracious_mcnulty 
```

## Excercise 1.2
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

```
## Excercise 1.3
```
$ docker run -it devopsdockeruh/pull_exercise
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```
## Excercise 1.4
```
$ docker run -d --name harjoitus devopsdockeruh/exec_bash_exercise
$ docker exec harjoitus tail -f ./logs.txt
Thu, 16 Apr 2020 11:45:22 GMT
Secret message is:
"Docker is easy"
```
## Excercise 1.5
```
$ docker run -d --name harjoitus ubuntu:16.04 sh -c 'while true; do date; sleep 1; done'
$ docker exec -it harjoitus bash
root@f884699b3aba:/# apt-get update
root@f884699b3aba:/# apt-get install curl
root@f884699b3aba:/# exit
$ docker exec -it harjoitus sh -c 'echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$websi
te;'
```

## Excercise 1.6
```
$ docker build -t docker-clock .
$ docker run docker-clock
```
```
FROM devopsdockeruh/overwrite_cmd_exercise
CMD ["--clock"]
```

## Excercise 1.7
```
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y curl
CMD echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;
```
```
$ docker build -t curler .
$ docker run -it curler
```

## Excercise 1.8
```
$ touch logs.txt
$ docker run -v $(pwd)/logs.txt:/usr/app/logs.txt devopsdockeruh/first_volume_exercise
```

## Excercise 1.9
```
$ docker run -d -p 80:80 devopsdockeruh/ports_exercise
(VM VirtualBoxin asetuksista port forwarding...)
$ curl localhost
Ports configured correctly!!
```

## Excercise 1.10
```
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y git && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash && apt install -y nodejs
RUN git clone https://github.com/Tambourin/frontend-example-docker.git
RUN cd /frontend-example-docker && npm install
RUN npm install -g serve
WORKDIR /frontend-example-docker
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 dist
```
### Excercise 1.11
```
FROM node
RUN apt-get update && apt-get install -y unzip curl
RUN curl -L -o master.zip https://github.com/docker-hy/backend-example-docker/archive/master.zip
RUN unzip master.zip
WORKDIR /backend-example-docker-master
RUN npm install
EXPOSE 8000
CMD npm start

$ docker run -v $(pwd)/logs.txt:/backend-example-docker-master/logs.txt -p 8000:8000 backend
```
### Excercise 1.12
```
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y git && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash && apt install -y nodejs
RUN git clone https://github.com/Tambourin/frontend-example-docker.git
WORKDIR /frontend-example-docker
RUN npm install && npm install -g serve
ENV API_URL=http://localhost:8000
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 dist

$ docker run -p 5000:5000 frnt
```
```
FROM node
RUN apt-get update && apt-get install -y unzip curl
RUN curl -L -o master.zip https://github.com/docker-hy/backend-example-docker/archive/master.zip
RUN unzip master.zip
WORKDIR /backend-example-docker-master
RUN npm install
EXPOSE 8000
ENV FRONT_URL=http://localhost:5000
CMD npm start

$ docker run -v $(pwd)/logs.txt:/backend-example-docker-master/logs.txt -p 8000:8000 backend
```
### Excercise 1.13
```
FROM openjdk:8
RUN apt-get update && apt-get install -y unzip curl
RUN curl -L -o master.zip https://github.com/docker-hy/spring-example-project/archive/master.zip
RUN unzip master.zip
WORKDIR /spring-example-project-master
RUN ./mvnw package
CMD java -jar ./target/docker-example-1.1.3.jar
```

### Excercise 1.14
```
FROM ruby:2.6.0
RUN apt-get update && apt-get install -y unzip curl nodejs
RUN curl -L -o master.zip https://github.com/docker-hy/rails-example-project/archive/master.zip
RUN unzip master.zip
WORKDIR /rails-example-project-master
RUN bundle install
RUN rails db:migrate
CMD rails s
```
### Excercise 1.15
```
https://hub.docker.com/repository/docker/tambourin/spring
```
### Excercise 1.16
```
https://docker-excercise.herokuapp.com/
```
### Excercise 1.17
Dockerfile. Start with Ubuntu and install Node and Npm
```
FROM ubuntu:16.04
EXPOSE 3000
WORKDIR /mydev
RUN apt-get update && apt-get install -y curl npm
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash && apt install -y nodejs
CMD npm start
```
Example: To start a React app (located in working directory) in a container following command can be used
```
$ docker run -p 3000:3000 -v $(pwd):/mydev devenv
```
### Excercise 1.18
```
version: '3.5'

services:
  first_volume:
    image: devopsdockeruh/first_volume_exercise
    volumes:
      - ./logs.txt:/usr/app/logs.txt
    container_name: first
```
### Excercise 1.19
```
version: '3'                                                                                                           
services:
  portter:
    image: devopsdockeruh/ports_exercise
    ports:
      -  80:80
```
### Excercise 1.20
```
version: '3.5'

services:
  front:
    build: ../front
    environment:
    -  API_URL=http://localhost:8000
    ports:
    -  5000:5000
  backend:
    build: ../back
    environment:                                                                                                       
    -  FRONT_URL=http://localhost:5000
    ports:
    -  8000:8000
```
