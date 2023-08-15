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

## 
