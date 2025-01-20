---
layout: post
title:  "Docker 筆記 - 常用指令"
date:   2025-01-20 12:41:00 +0800
categories: Docker
---

## 名詞解釋

-   **_Images_** - The blueprints of our application which form the basis of containers. In the demo above, we used the docker pull command to download the **busybox** image.

-   **_Containers_** - Created from Docker images and run the actual application. We create a container using docker run which we did using the busybox image that we downloaded. A list of running containers can be seen using the docker ps command.

-   **_Docker Daemon_** - The background service running on the host that manages building, running and distributing Docker containers. The daemon is the process that runs in the operating system which clients talk to.

-   **_Docker Client_** - The command line tool that allows the user to interact with the daemon. More generally, there can be other forms of clients too - such as [Kitematic](https://kitematic.com/) which provide a GUI to the users.

-   **_Docker Hub_** - A [registry](https://hub.docker.com/explore/) of Docker images. You can think of the registry as a directory of all available Docker images. If required, one can host theirz own Docker registries and can use them for pulling images.

---

### 查閱文件
```bash
$ docker --help

$ docker ps --help

$ docker run --help
```

### 搜尋 Docker Hub 上的 Image
```bash
$ docker search elasticsearch

NAME DESCRIPTION STARS OFFICIAL AUTOMATED

elasticsearch Elasticsearch is a powerful open source se... 697 [OK]
itzg/elasticsearch Provides an easily configurable Elasticsea... 17 [OK]
tutum/elasticsearch Elasticsearch image - listens in port 9200. 15 [OK]
barnybug/elasticsearch Latest Elasticsearch 1.7.2 and previous re... 15 [OK]
digitalwonderland/elasticsearch Latest Elasticsearch with Marvel & Kibana 12 [OK]
monsantoco/elasticsearch ElasticSearch Docker image 9 [OK]
```

### 從 Docker Hub 拉 Image
```bash
$ docker pull busybox
```

### 查看本地 Images
```bash
$ docker images

REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE

busybox latest c51f86c28340 4 weeks ago 1.109 MB

```

### 把 Image 跑起來
```bash
$ docker run busybox
```

### 把 Image 跑起來順便執行指令
```bash
$ docker run busybox echo "hello from busybox"

hello from busybox
```

### 跑起來結束後自動刪除 Container
```bash
$ docker run --rm prakhar1989/static-site
```

### Build Image
```bash
$ docker build -t yourusername/catnip .

Sending build context to Docker daemon 8.704 kB

Step 1 : FROM python:3

# Executing 3 build triggers...

Step 1 : COPY requirements.txt /usr/src/app/

---> Using cache

Step 1 : RUN pip install --no-cache-dir -r requirements.txt

---> Using cache

Step 1 : COPY . /usr/src/app

---> 1d61f639ef9e

Removing intermediate container 4de6ddf5528c

Step 2 : EXPOSE 5000

---> Running in 12cfcf6d67ee

---> f423c2f179d1

Removing intermediate container 12cfcf6d67ee

Step 3 : CMD python ./app.py

---> Running in f01401a5ace9

---> 13e87ed1fbc2

Removing intermediate container f01401a5ace9

Successfully built 13e87ed1fbc2
```


## 查看 Containers（預設僅顯示 Running 中的）
```bash
$ docker ps

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
```

## 查看所有 Containers
```bash
$ docker ps -a

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES

305297d7a235 busybox "uptime" 11 minutes ago Exited (0) 11 minutes ago distracted_goldstine
ff0a5c3750b9 busybox "sh" 12 minutes ago Exited (0) 12 minutes ago elated_ramanujan
14e5bd11d164 hello-world "/hello" 2 minutes ago Exited (0) 2 minutes ago thirsty_euclid
```

## 跑起來並且進入 terminal
``` bash
$ docker run -it busybox sh

/ # ls

bin dev etc home proc root sys tmp usr var

/ # uptime

05:45:21 up 5:58, 0 users, load average: 0.00, 0.01, 0.04
```


## 進入已跑起來的 container 的 terminal
```bash
$ sudo docker exec -it gitlab bash
```


## 跑起一個靜態 Web app

跑起後執行在背景，並且發布所有已公開的 port，並可以 --name 來命名。
```shell
$ docker run -d -P --name static-site prakhar1989/static-site

e61d12292d69556eabe2a44c16cbd54486b2527e2ce4f95438e504afb7b02810
```


## 查看公開的 port
```shell
$ docker port static-site

80/tcp -> 0.0.0.0:32769

443/tcp -> 0.0.0.0:32768

```

## 指定 port
```shell
$ docker run -p 8888:80 prakhar1989/static-site

Nginx is running...
```

## 停止一個跑起來的 container
```shell
$ docker stop static-site

static-site
```

### 刪除 Containers
```shell
$ docker rm 305297d7a235 ff0a5c3750b9

305297d7a235

ff0a5c3750b9
```


### 刪除所有 Containers
```shell
$ docker rm $(docker ps -a -q -f status=exited)
```

### 刪除所有容器
```shell
$ docker container prune

WARNING! This will remove all stopped containers.

Are you sure you want to continue? [y/N] y

Deleted Containers:

4a7f7eebae0f63178aff7eb0aa39f0627a203ab2df258c1a00b456cf20063
f98f9c2aa1eaf727e4ec9c0283bcaa4762fbdba7f26191f26c97f64090360

Total reclaimed space: 212 B
```
