---
layout: post
title: Docker-Compose - Wait for Dependencies
date: 2021-06-23 12:54 +0545
categories: docker-compose
tags: development
---

`Don't Repeat Yourself` for sake of others. So, there is nothing new in here, that hasn't
been elaborated on by many other authors. I am just noting it down and extracting major
parts of it.

So, let's wait for the docker dependencies...

If we run [docker-compose.yml](https://github.com/ashimjk/docker-compose-wait/blob/master/docker-compose.yml) then
`waiting-for-rabbitmq` will print
`Waiting for rabbitmq...` until `rabbitmq` starts. Sometimes it's expected behavior but not for us.

There are many ways to resolve it and we will look into this one by one.

## Approach 1: Run the dependencies first

First, we will run the dependency along with its status checker service. Once the status is green,
then we will run all the other dependent services.

Let's create a scenario, where we will have a `web` service that will depend on `rabbitmq`.
Now we will add another service in
the [docker-compose.yml](https://github.com/ashimjk/docker-compose-wait/blob/master/approach-1/docker-compose.yml) file,
which is like this.

```yml
services:
  rabbitmq:
    container_name: ajk_rabbitmq
    image: rabbitmq:3.7-management-alpine
    ports:
      - 5672:5672
      - 15672:15672

  web:
    container_name: ajk_web
    image: alpine:3.8
    command: [ 'sh', '-c', 'nc -z -v rabbitmq 5672' ]
    depends_on:
      - rabbitmq
    links:
      - rabbitmq

  waiting_for_rabbitmq:
    container_name: ajk_waiting_for_rabbitmq
    image: alpine:3.8
    command: [ 'sh', '-c', 'until nc -z -v rabbitmq 5672; do echo "Waiting for rabbitmq..." && sleep 1; done;' ]
    depends_on:
      - rabbitmq
    links:
      - rabbitmq
```

### How to run

- Run `docker-compose up waiting_for_rabbitmq` command
    - It will start `rabbitmq` service
    - `waiting_for_rabbitmq` service will wait for `rabbitmq` to be started
    - Once, it is started, it will print `5672 (IP:5672) open` in the console
- Now, run `docker-compose up web`

Although it's a sequential approach it will do the work for us.

Reference:

- [Article](https://8thlight.com/blog/dariusz-pasciak/2016/10/17/docker-compose-wait-for-dependencies.html)
- [GitHub](https://github.com/dadarek/docker-wait-for-dependencies)

## Approach 2: Using health checks

When we
run [docker-compose.yml](https://github.com/ashimjk/docker-compose-wait/blob/master/approach-2/docker-compose.yml) file,
first it will run `rabbitmq` service,
and will wait for its status to be `healthy`.

Once, it's ready, `web` service will be started and will print the following in the console:

```
ajk_web     | rabbitmq (172.22.0.2:5672) open
ajk_web       exited with code 0
```

```yml
services:
  rabbitmq:
    container_name: ajk_rabbitmq
    image: rabbitmq:3.7-management-alpine
    ports:
      - 5672:5672
      - 15672:15672
    healthcheck:
      test: ["CMD", "nc", "-z", "localhost", "5672"]
      interval: 10s
      timeout: 10s
      retries: 3

  web:
    container_name: ajk_web
    image: alpine:3.8
    command: ["sh", "-c", "nc -z -v rabbitmq 5672"]
    depends_on:
      rabbitmq:
        condition: service_healthy
    links:
      - rabbitmq
```

Here, we are using `healthcheck` configuration option, added in `rabbitmq` service. `web` service is depending upon
`rabbitmq`  with the condition `service_healthy`.

Note: docker-compose version 3 doesn't support the condition form of `depends_on`. We can add `restart: on-failure`
in the `web` service to get the same behavior. One thing to keep in mind is, now `web` service will be restarted
until `rabbitmq` status is healthy.

Reference:

- [Health Checks](https://docs.docker.com/compose/compose-file/compose-file-v3/#healthcheck)
- [Depends-On V2](https://docs.docker.com/compose/compose-file/compose-file-v2/#depends_on)
- [Depends-On V3](https://docs.docker.com/compose/compose-file/compose-file-v3/#depends_on)

## Other ways to do it

- [wait-for-it](https://github.com/vishnubob/wait-for-it)
- [dockerize](https://github.com/jwilder/dockerize)
- [goss](https://github.com/aelsabbahy/goss)
- [Health Check using Goss](https://medium.com/@aelsabbahy/docker-1-12-kubernetes-simplified-health-checks-and-container-ordering-with-goss-fa8debbe676c)
- [Start Up Order](https://docs.docker.com/compose/startup-order/)
- [docker-compose-wait](https://github.com/ufoscout/docker-compose-wait)
- [DOCKER COMPOSE WAIT FOR DEPENDENCIES](https://www.datanovia.com/en/courses/docker-compose-wait-for-dependencies/)
