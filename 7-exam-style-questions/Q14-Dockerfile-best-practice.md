## Question - Dockerfile best practice

### Docker and container sec docs (can't be used in exam)

- [Google Docker best practices](https://cloud.google.com/blog/products/containers-kubernetes/7-best-practices-for-building-containers)
- [Docker](https://learnk8s.io/blog/smaller-docker-images)

Given a Dockerfile, analyse it and update it based on security best practices.

#### 1 - Open Dockerfile and fix security issues

```sh

vi /root/Dockerfile

FROM ubuntu:latest

ENV CI=true

RUN apt get update
RUN apt get instal -y wget
RUN apt get install -y curl

USER root

WORKDIR /code
COPY package.json package-lock.json /code/
RUN npm ci
COPY src /code/src

CMD [ "npm", "start" ]

```

#### 2 - Update Dockerfile with best practices

```sh


FROM ubuntu:20:04 ## updated image to a specific version

ENV CI=true

RUN apt get update
RUN apt get install -y wget curl ## lighter image due to docker caching

USER user ## no privileged user being used

WORKDIR /code
COPY package.json package-lock.json /code/
RUN npm ci
COPY src /code/src

CMD [ "npm", "start" ]

```
