![Docker Compose UI](https://raw.githubusercontent.com/francescou/docker-compose-ui/master/static/images/logo.png)

## What is it

Docker Compose UI is a web interface for Docker Compose.

If you plan to use Docker Compose UI in a production environment please check the warning on the [Docker Compose docs](https://docs.docker.com/compose/#build-and-run-your-app-with-compose#overview-of-docker-compose): _Compose is great for development environments, staging servers, and CI. We don’t recommend that you use it in production yet._

![screenshot project detail](https://raw.githubusercontent.com/francescou/docker-compose-ui/master/screenshots/project-detail.png)


![screenshot remote docker host](https://raw.githubusercontent.com/francescou/docker-compose-ui/master/screenshots/remote-host.png)

![screenshot project logs](https://raw.githubusercontent.com/francescou/docker-compose-ui/master/screenshots/logs.png)

## Requirements

[Docker 1.7.1 or later](https://github.com/docker/compose/releases/tag/1.4.2)

## Getting started

Run the following command in terminal:

    docker run \
    --name docker-compose-ui \
    -p 5000:5000 \
    -v /var/run/docker.sock:/var/run/docker.sock \
    francescou/docker-compose-ui

You have to wait while Docker pulls the container from the Docker Hub: https://registry.hub.docker.com/u/francescou/docker-compose-ui

Then open your browser to `http://localhost:5000`

### Add your own docker-compose projects

If you want to use your own docker-compose projects, put them into a directory */home/user/docker-compose-ui/demo-projects* and then run:

    docker run \
        --name docker-compose-ui \
        -p 5000:5000 \
        -v /home/user/docker-compose-ui/demo-projects:/opt/docker-compose-projects:ro \
        -v /var/run/docker.sock:/var/run/docker.sock \
        francescou/docker-compose-ui

you can download my example projects into */home/user/docker-compose-ui/demo-projects/* from https://github.com/francescou/docker-compose-ui/tree/master/demo-projects

### Note about scaling services

Note that some of the services provided by the demo projects are not "scalable" with `docker-compose scale SERVICE=NUM` because of published ports conflicts.

Check out this project if you are interested in scaling up and down a docker-compose service without having any down time: https://github.com/francescou/consul-template-docker-compose


### Note about volumes

since you're running docker-compose inside a container you must pay attention to volumes mounted with relative paths, see [Issue #6](https://github.com/francescou/docker-compose-ui/issues/6)

## Remote docker host

You can also run containers on a remote docker host, e.g.

    docker run \
        --name docker-compose-ui \
        -p 5000:5000 \
        -v /home/user/docker-compose-ui/demo-projects:/opt/docker-compose-projects:ro \
        -e DOCKER_HOST=remote-docker-host:2375 \
        francescou/docker-compose-ui


### HTTPS Remote docker host

You need to add two environment properties to use an HTTPS remote docker host: `DOCKER_CERT_PATH` and `DOCKER_TLS_VERIFY`, see [example by @ymote](https://github.com/francescou/docker-compose-ui/issues/5#issuecomment-135697832)

## Technologies

Docker Compose UI has been developed using Flask (python microframework) to provide RESTful services and AngularJS to implement the Single Page Application web ui.

The application uses [Docker Compose](https://docs.docker.com/compose) to monitor and edit the state of a set of docker compose projects (*docker-compose.yml* files).


## API

### list docker compose projects

    curl http://localhost:5000/api/v1/projects

### show docker compose "hello-node" project details

    curl http://localhost:5000/api/v1/projects/hello-node

### get the content of docker-compose.yml in project "hello-node"

    curl http://localhost:5000/api/v1/projects/yml/hello-node

### docker inspect the container "hellonode_hello_1" in project "hello-node"

    curl http://localhost:5000/api/v1/projects/hello-node/hellonode_hello_1

### docker-compose up of project "hello-node"

    curl -X POST http://localhost:5000/api/v1/projects --data '{"id":"hello-node"}' -H'Content-type: application/json'

### docker-compose scale redis=2, project "node-redis"

    curl -X PUT http://localhost:5000/api/v1/services --data '{"service":"redis","project":"node-redis","num":"2"}' -H'Content-type: application/json'

### docker-compose start of project "hello-node"

    curl -X POST http://localhost:5000/api/v1/start --data '{"id":"hello-node"}' -H'Content-type: application/json'

### docker-compose stop of project "hello-node"

    curl -X POST http://localhost:5000/api/v1/stop --data '{"id":"hello-node"}' -H'Content-type: application/json'

### docker-compose build of project "hello-node"

    curl -X POST http://localhost:5000/api/v1/build --data '{"id":"hello-node"}' -H'Content-type: application/json'

### docker-compose update of project "hello-node"

    curl -X PUT http://localhost:5000/api/v1/projects --data '{"id":"hello-node"}' -H'Content-type: application/json'

### docker-compose kill of project "hello-node"

    curl -X DELETE http://localhost:5000/api/v1/projects/hello-node

### docker-compose logs of project "hello-node", 100 lines limit

    curl http://localhost:5000/api/v1/logs/hello-node/100

### docker-compose logs of container hellonode_hello_1 in project "hello-node", 100 lines limit

    curl http://localhost:5000/api/v1/logs/hello-node/hellonode_hello_1/100

### authentication status (DEPRECATED)

    curl http://localhost:5000/api/v1/authentication

### set password (DEPRECATED)

    curl -X POST -u admin  http://localhost:5000/api/v1/authentication -H 'Content-type: application/json' --data  '{"username":"admin", "password":"password"}'

### disable basic authentication (DEPRECATED)

    curl -X DELETE -u admin  http://localhost:5000/api/v1/authentication


## Issues

If you have any problems with or questions about this image, please open a GitHub issue on https://github.com/francescou/docker-compose-ui

## License - MIT

The Docker Compose UI code is licensed under the MIT license.

Docker Compose UI: Copyright (c) 2015 Francesco Uliana. www.uliana.it/francesco

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.


