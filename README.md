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

