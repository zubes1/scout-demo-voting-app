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

List of Known Vulnerabilities:

```
docker scout quickview scout-demo-voting-app-result
docker scout quickview scout-demo-voting-app-vote
docker scout quickview scout-demo-voting-app-worker
```


| S. No  |  Name of Service | Detected Vulnerabilites | Status |
|----------|------------|---------| -----------|
|  1       | redis      |  0C, 0H, 0M, 0L | Good |
|  2       | Postgres   |  0C, 0H, 0M, 0L | Good |
|  3       | Vote(Python)       |  0C, 1H, 4M, 11L | Need fix |
|  4       | Result(Node)     | 2C, 3H, 1M, 24L  | Need fix|
|  5       | Worker(.Net)     |  1C, 0H, 0M, 25L | Need fix|


## Scout for Vote


Let's first look at the front-end web app service "scout-demo-voting-app-vote-1" first.


<img width="1142" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/82ec2cd4-9d16-473c-974e-3305c1c3a676">





<img width="1178" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/b4569be2-66b1-462a-9a59-d8a142090880">


Click on "Recommendation Fixes"


<img width="1189" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/2c71de3f-cf7e-452e-b768-00552476e5fd">


Select the tag you would like to see recommendations for. The list displays new recommended tags in descending order, where the top results are rated as most suitable.



Let's pick python:3.11-slim that fixes most of the C and H labelled vulnerabilities.

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



## Scout for Worker


Initial Status:

```
docker scout quickview scout-demo-voting-app-worker
INFO New version 0.22.3 available (installed version is 0.20.0)
    ✓ Image stored for indexing
    ✓ Indexed 160 packages

  Your image  scout-demo-voting-app-worker  │    1C     0H     0M    25L
  Base image  debian:11-slim                │    0C     0H     0M    25L
  Updated base image  debian:stable-slim    │    0C     0H     0M    17L
                                            │                         -8
```

<img width="683" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/e9b1b12c-d82b-44e4-8b65-aa3d07b21547">


## Scout for Results


Initial Status:

```
docker scout quickview scout-demo-voting-app-result
INFO New version 0.22.3 available (installed version is 0.20.0)
    ✓ SBOM of image already cached, 525 packages indexed

  Your image  scout-demo-voting-app-result  │    2C     3H     1M    24L
  Base image  node:18-bookworm-slim         │    0C     0H     1M    17L
  Updated base image  node:20-slim          │    0C     0H     0M    17L
                                            │                  -1
```



Let's fix by modifying package.json, replacing socket.io version from "^2.5.0" to "^4.0.5"

```
 docker compose down
 docker compose up -d --build
```

<img width="1241" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/665249be-843f-4141-a830-22a401d3a2b9">


## Using Scout CLI


## Introduce SBOMs and Provenance

SBOMs and provenance are two important concepts in software supply chain security.

- SBOMs (Software Bill of Materials) are a list of all the components that make up a software project. This includes the software code, as well as the libraries and dependencies that the code uses. SBOMs can be used to track the origin of software components and to identify potential security vulnerabilities.
- Provenance is a record of the steps that were taken to create a software project. This includes the code that was written, the tools that were used, and the people who were involved. Provenance can be used to understand how a software project was created and to identify potential security vulnerabilities.

```
 cd scout-demo-voting-app/result
 ````

The command ```docker buildx b . -f Dockerfile --sbom=1 --provenance=1 -t ajeetraina/scout-demo-voting-app-result:attest --push``` will build a Docker image with both an SBOM and provenance. This will allow you to track the origin of the software components in the image and to identify potential security vulnerabilities.


```
 docker buildx b . -f Dockerfile --sbom=1 --provenance=1 -t ajeetraina/scout-demo-voting-app-result:attest --push
```

## Using Attestations

Attestations are cryptographically signed statements that verify the provenance of a software artifact. 
They can be used to ensure that the software artifact has not been tampered with and that it has been built from a trusted source.

When you run the command ```docker scout cves ajeetraina/scout-demo-voting-app-result:attest```, Docker Scout will use attestations to verify the provenance of the image. This means that Docker Scout will check to make sure that the image has been built from a trusted source and that it has not been tampered with.

```
docker scout cves ajeetraina/scout-demo-voting-app-result:attest --ignore-base
```

```
INFO New version 0.22.3 available (installed version is 0.20.0)
    ✓ Provenance obtained from attestation
    ✓ SBOM obtained from attestation, 520 packages indexed
    ✓ Pulled
    ✗ Detected 20 vulnerable packages with a total of 28 vulnerabilities

   1C     1H     0M     0L  socket.io-parser 3.4.2
pkg:npm/socket.io-parser@3.4.2

Dockerfile (17:19)
RUN npm ci \
...
...

12 vulnerabilities found in 6 packages
  LOW       7
  MEDIUM    0
  HIGH      3
  CRITICAL  2
```

## Update socket.io and run npm install

Open package.json under result/ and change socket.io version to "4.05"
Don't forget to run ```npm install```



```
docker build . -t ajeetrainascout-demo-voting-app-result:fix --load
```

<img width="1260" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/1bd22474-5fa9-4f3f-98f2-b1363c27e9bb">

## Use Recommendation and update base image

Open Dockerfile under result/ and change it to 

```
 FROM node:20-slim
```


## Re-build the Docker Image

```
 docker build . -t ajeetraina/scout-demo-voting-app-result:fix --load
```

<img width="1246" alt="image" src="https://github.com/ajeetraina/scout-demo-voting-app/assets/313480/1655fea9-6128-4eb1-a5e7-fa3522044e5d">


##

```
docker scout cves ajeetraina/scout-demo-voting-app-result:fix --ignore-base
    ✓ SBOM of image already cached, 497 packages indexed
    ✓ Ignoring packages and vulnerabilities from base image node
    ✗ Detected 3 vulnerable packages with a total of 7 vulnerabilities


...
....


7 vulnerabilities found in 3 packages
  LOW       7
  MEDIUM    0
  HIGH      0
  CRITICAL  0

What's Next?
  Learn more about base image update recommendations → docker scout recommendations ajeetraina/scout-demo-voting-app-result:fix
```

## Compare to image in prod

```
 docker scout compare ajeetraina/scout-demo-voting-app-result:fix --to-stream prod
```

## Integration with Github Action

There is already a workflow file created for vote, worker and result [here](https://github.com/dockersamples/scout-demo-voting-app/tree/main/.github/workflows). 
Ensure that you make the following changes:
- Replace IMAGE_NAME with your Docker Hub ID
- Add DOCKER_HUB_USER and DOCKER_USER_PAT in GitHub Repo settings

```
 env:
  IMAGE_NAME: yournamespace/scout-demo-voting-app-result
  SHA: ${{ github.event.pull_request.head.sha || github.event.after }}
  DOCKERFILE_PATH: vote/Dockerfile
```

