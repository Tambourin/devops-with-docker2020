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
### Excercise 2.1
```
version: '3.5'

services:
  first_volume:
    image: devopsdockeruh/first_volume_exercise
    volumes:
      - ./logs.txt:/usr/app/logs.txt
    container_name: first
```
### Excercise 2.2
```
version: '3'                                                                                                           
services:
  portter:
    image: devopsdockeruh/ports_exercise
    ports:
      -  80:80
```
### Excercise 2.3
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
### Excercise 2.4
```
$ docker-compose up --scale compute=2
```
### Excercise 2.5
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
    -  REDIS=redis
    ports:
    -  8000:8000
  redis:
    image: redis
```
### Excercise 2.6
```
version: '3.5'
services:
  front:
    build: ../frontend
    environment:
    -  API_URL=backend
    ports:
    -  5000:5000
  backend:
    build: ../backend
    environment:
    - FRONT_URL=http://localhost:5000
    - DB_USERNAME=example
    - DB_PASSWORD=example
    - DB_HOST=db
    - REDIS=redis
    ports:
    -  8000:8000
    depends_on:
    - db
  db:
    image: postgres
    restart: unless-stopped
    environment:
     - POSTGRES_USER=example
     - POSTGRES_PASSWORD=example
  redis:
    image: redis
```
### Excercise 2.7
```
version: '3.5'
services:
  front:
    image: kurkkufront
    ports:
    -  3000:3000
  back:
    image: kurkkuback
    ports:
    -  5000:5000
    volumes:
    -  models:/src/model
  training:
    image: kurkkutraining
    build: https://github.com/docker-hy/ml-kurkkumopo-training.git
    volumes:
    -  models:/src/model
    -  images:/src/imgs
volumes:
  models:
  images:
```
### Excrcise 2.8
```
version: '3.5'
services:
  nginx:
    image: nginx
    volumes:
    - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports: 
    - 80:80
    depends_on:
      - front
      - backend
  front:
    build: ../frontend
    environment:
    -  API_URL=backend
  backend:
    build: ../backend
    environment:
    - FRONT_URL=front
    - DB_USERNAME=example
    - DB_PASSWORD=example
    - DB_HOST=db
    - REDIS=redis
    depends_on:
    - db
    - redis
  db:
    image: postgres
    restart: unless-stopped
    environment:
     - POSTGRES_USER=example
     - POSTGRES_PASSWORD=example
  redis:
    image: redis
```
### Excercise 2.9
```
version: '3.8'
services:
  front:
    build: ../frontend
    environment:
    -  API_URL=http://localhost:8000
    ports:
    -  5000:5000
  backend:
    build: ../backend
    environment:
    - FRONT_URL=http://localhost:5000
    - DB_USERNAME=example
    - DB_PASSWORD=example
    - DB_HOST=db
    - REDIS=redis
    ports:
    -  8000:8000
    depends_on:
    - db
  db:
    image: postgres
    restart: unless-stopped
    environment:
     - POSTGRES_USER=example
     - POSTGRES_PASSWORD=example
    volumes: 
    - ./database:/var/lib/postgresql/data
  redis:
    image: redis
    volumes: 
    - ./redis_data:/data
```
### Excercise 2.10
Some how it has started to work along the way... I think it is because backend url is now not given as ENV so it uses the right path.
```
version: '3.5'
services:
  nginx:
    image: nginx
    volumes:
    - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports: 
    - 80:80
    depends_on:
      - front
      - backend
  front:
    build: ../frontend
  backend:
    build: ../backend
    environment:
    - DB_USERNAME=example
    - DB_PASSWORD=example
    - DB_HOST=db
    - REDIS=redis
    depends_on:
    - db
    - redis
  db:
    image: postgres
    restart: unless-stopped
    environment:
     - POSTGRES_USER=example
     - POSTGRES_PASSWORD=example
  redis:
    image: redis
```
```
FROM node
RUN apt-get update && apt-get install -y git && apt-get install -y curl
RUN git clone https://github.com/Tambourin/frontend-example-docker.git
RUN cd /frontend-example-docker && npm install
RUN npm install -g serve
WORKDIR /frontend-example-docker
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 dist
```
```
FROM node
RUN apt-get update && apt-get install -y unzip curl
RUN curl -L -o master.zip https://github.com/docker-hy/backend-example-docker/archive/master.zip
RUN unzip master.zip
WORKDIR /backend-example-docker-master
RUN npm install
EXPOSE 8000
CMD npm start
```
### Excercise 3.1
front: 1 210 MB -> 242 MB
back: 997 MB -> 208 MB
```
FROM node:slim

ENV API_URL=http://localhost:8000

RUN apt-get update && apt-get install -y git curl && \
    git clone https://github.com/Tambourin/frontend-example-docker.git && \
    cd /frontend-example-docker && \
    npm install && npm install -g serve && \
    npm run build && \
    apt-get purge -y --auto-remove git curl && \
    mv dist ../ && cd .. && rm -r -f frontend-example-docker/

WORKDIR /
EXPOSE 5000

CMD serve -s -l 5000 dist
```
```
FROM node:slim

RUN apt-get update && \
  apt-get install -y unzip curl && \
  curl -L -o master.zip https://github.com/docker-hy/backend-example-docker/archive/master.zip && \
  unzip master.zip && cd backend-example-docker-master && \
  npm install && \
  apt-get purge -y --auto-remove curl unzip && \
  rm -rf /var/lib/apt/lists/*

WORKDIR /backend-example-docker-master

CMD npm start
```
### Excercise 3.2
https://github.com/Tambourin/docker-excercise

### Excercise 3.3
Run with: docker run -it -v /var/run/docker.sock:/var/run/docker.sock image

App:
```
echo "git url:"
read input
echo "create directory appdir"
echo "cloning..."
git clone ${input} ./appdir
echo "building..."
docker build -t tambourin/testapp ./appdir
docker login 
docker push tambourin/testapp
```
Dockerfile:
```
FROM ubuntu

COPY . ./app

RUN apt-get update && apt-get install -y git curl
RUN curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh

WORKDIR /app

CMD ./dockerbuilder
```


### Excercise 3.4
```
FROM node:slim

ENV API_URL=http://localhost:8000

RUN apt-get update && apt-get install -y git curl && \
    git clone https://github.com/Tambourin/frontend-example-docker.git && \
    cd /frontend-example-docker && \
    npm install && npm install -g serve && \
    npm run build && \
    apt-get purge -y --auto-remove git curl && \
    mv dist ../ && cd .. && rm -r -f frontend-example-docker && \
    useradd -m app 

USER app
WORKDIR /
EXPOSE 5000

CMD echo current user: `whoami` && serve -s -l 5000 dist
```
```
FROM node:slim

RUN apt-get update && \
  apt-get install -y unzip curl && \
  curl -L -o master.zip https://github.com/docker-hy/backend-example-docker/archive/master.zip && \
  unzip master.zip && cd backend-example-docker-master && \
  npm install && \
  apt-get purge -y --auto-remove curl unzip && \
  rm -rf /var/lib/apt/lists/*
RUN useradd -m appuser && chown -R appuser /backend-example-docker-master

USER appuser

WORKDIR /backend-example-docker-master

CMD echo current user: `whoami` && echo folder privileges: `ls -l ./` && npm start
```
### Excercise 3.5
front 242MB -> 173MB
back 208MB -> 182MB
```
FROM node:alpine

ENV API_URL=http://localhost:8000

RUN apk add --no-cache git && \
    git clone https://github.com/Tambourin/frontend-example-docker.git && \
    cd /frontend-example-docker && \
    npm install && npm install -g serve && \
    npm run build && \
    apk del git && \
    mv dist ../ && cd .. && rm -r -f frontend-example-docker

USER node
WORKDIR /
EXPOSE 5000

CMD echo current user: `whoami` && serve -s -l 5000 dist
```
```
FROM node:alpine

RUN apk add --no-cache git && \
  git clone https://github.com/docker-hy/backend-example-docker.git && \
  cd /backend-example-docker && \
  npm install && \
  apk del git
RUN chown -R node /backend-example-docker

USER node

WORKDIR /backend-example-docker

CMD echo current user: `whoami` && echo folder privileges: `ls -l ./` && npm start
```
### Excercise 3.6
173MB -> 126MB
```
FROM node as build-stage

ENV API_URL=http://localhost:8000
RUN git clone https://github.com/Tambourin/frontend-example-docker.git && \
    cd /frontend-example-docker && \
    npm install && \
    npm run build

FROM node:alpine

COPY --from=build-stage /frontend-example-docker/dist/ /dist
RUN npm install -g serve

USER node
WORKDIR /
EXPOSE 5000

CMD echo current user: `whoami` && serve -s -l 5000 dist
```
### Excercise 3.7
613MB -> 123MB

Old:
```
FROM openjdk:8
RUN apt-get update && apt-get install -y unzip curl
RUN curl -L -o master.zip https://github.com/docker-hy/spring-example-project/archive/master.zip
RUN unzip master.zip
WORKDIR /spring-example-project-master
RUN ./mvnw package
CMD java -jar ./target/docker-example-1.1.3.jar
```
new:
```
FROM openjdk:8 as build-stage
RUN git clone https://github.com/docker-hy/spring-example-project.git
WORKDIR /spring-example-project
RUN ./mvnw package

FROM openjdk:8-alpine
COPY --from=build-stage /spring-example-project/target/docker-example-1.1.3.jar example.jar
RUN adduser -D appuser
USER appuser
CMD java -jar example.jar
```

