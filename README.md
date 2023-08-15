# Docker Scout Voting App Demo

A repository containing a Voting application and Dockerfile to demonstrate the use of Docker Scout to analyze and remediate CVEs in a container image. The application consists of Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) which collects new votes
* A [.NET](/worker/) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
* A [Node.js](/result) web app which shows the results of the voting in real time



## Getting started

- Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows.
- [Docker Compose](https://docs.docker.com/compose) will be automatically installed.
- On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).


Run in this directory to build and run the app:

```shell
docker compose up
```

<img width="1245" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/9874d3e1-ece3-4fbe-85e4-ec8db08014df">


The `vote` app will be running at [http://localhost:5000](http://localhost:5000), and the `results` will be at [http://localhost:5001](http://localhost:5001).

## Docker Scout in Action

Let's first look at the front-end web app service "scout-demo-voting-app-vote-1" first.


<img width="1142" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/82ec2cd4-9d16-473c-974e-3305c1c3a676">





<img width="1178" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/b4569be2-66b1-462a-9a59-d8a142090880">


Click on "Recommendation Fixes"


<img width="1189" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/2c71de3f-cf7e-452e-b768-00552476e5fd">


Select the tag you would like to see recommendations for. The list displays new recommended tags in descending order, where the top results are rated as most suitable.


<img width="951" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/b463add9-27c4-488c-a091-3532db432950">



As you can see that the 3.11.4-alpine or 3.11.4-alpine3.1 is one image tag that fixes almost all high, critical, medium and low severity.
Let's pick it up and update our Dockerfile accordingly, replacing 3.9-slim with 3.11.4-alpine base image.

```
# Using official python runtime base image
#FROM python:3.9-slim

FROM python:3.11-slim
```

Don't forget to rebuild the container image by running the following command:

```
 docker compose up -d --build
```

<img width="1507" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/df19c095-ff43-4a00-b60c-b3896ea6b4be">
