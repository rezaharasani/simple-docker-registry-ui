# Simple Docker Registry UI
Setup Simple Docker Registry UI for Simple Jobs

<p align="center">
  <img src="https://camo.githubusercontent.com/785c9401ae7cc0996bdc1eb3e84ec707e895c8b8d12822d5fe120293bdba69ab/68747470733a2f2f7261772e6769746875622e636f6d2f4a6f7869742f646f636b65722d72656769737472792d75692f6d61696e2f646f636b65722d72656769737472792d75692e676966" width="800"/>
</p>

Configure own Docker Registry with 3rd party UI manager: https://github.com/Joxit/docker-registry-ui


## Overview
This project aims to provide a simple and complete user interface for your private docker registry. You can customize the interface with various options.


## Recommended Docker Registry Usage
Here is a simple usage of Docker Registry UI with Docker Registry Server using docker-compose. This example should work for most of your use case and your UI will be on the same domain as you registry.
```
version: '3.8'

services:
  registry-ui:
    image: joxit/docker-registry-ui:2.5.6
    restart: always
    ports:
      - 80:80
    environment:
      - SINGLE_REGISTRY=true
      - REGISTRY_TITLE=Docker Registry UI
      - DELETE_IMAGES=true
      - SHOW_CONTENT_DIGEST=true
      - NGINX_PROXY_PASS_URL=http://registry-server:5000
      - SHOW_CATALOG_NB_TAGS=true
      - CATALOG_MIN_BRANCHES=1
      - CATALOG_MAX_BRANCHES=1
      - TAGLIST_PAGE_SIZE=100
      - REGISTRY_SECURED=false
      - CATALOG_ELEMENTS_LIMIT=1000
    container_name: registry-ui

  registry-server:
    image: registry:2
    restart: always
    environment:
      REGISTRY_HTTP_HEADERS_Access-Control-Allow-Origin: '[http://172.16.188.2]'
      REGISTRY_HTTP_HEADERS_Access-Control-Allow-Methods: '[HEAD,GET,OPTIONS,DELETE]'
      REGISTRY_HTTP_HEADERS_Access-Control-Allow-Credentials: '[true]'
      REGISTRY_HTTP_HEADERS_Access-Control-Allow-Headers: '[Authorization,Accept,Cache-Control]'
      REGISTRY_HTTP_HEADERS_Access-Control-Expose-Headers: '[Docker-Content-Digest]'
      REGISTRY_STORAGE_DELETE_ENABLED: 'true'
    volumes:
      - ./registry/data:/var/lib/registry
    container_name: registry-server
```

**Note:** For local usage, we used **localhost** keyword, and for remote usage, we used **{DOCKER_REGISTRY_IP_ADDRESS}** keyword. Also, the **|** sign means you should used one of *localhost* or *DOCKER_REGISTRY_IP_ADDRESS*, not both of them.

##  Configure custom registry
  - Edit Docker config `/etc/docker/daemon.json`
  - Modify file `docker.json`
    ```
    {
      "insecure-registries" : ["{localhost:5000 | DOCKER_REGISTRY_IP_ADDRESS}"]
    }
    ```
  - Restat Docker `sudo service restart docker`


## Working with Local Docker Registry
### Pull and push custome docker images
  -  Pull example image: `docker pull busybox
  -  Create new version of busybox with *NEW_VERSION* tag which represents URL of custom Docker Registry: `docker tag busybox localhost:5000/busybox:{NEW_VERSION} | {DOCKER_REGISTRY_IP_ADDRESS}/busybox:{NEW_VERSION}`
  -  Remove old image: `docker rmi busybox`
  -  Publish new image to custom Docker Registry `docker push localhost:5000/busybox:{NEW_VERSION} | {DOCKER_REGISTRY_IP_ADDRESS}/busybox:{NEW_VERSION}`

### Remove and push new images
  - Remove pushed image: `docker rmi localhost:5000/busybox:{NEW_VERSION} | {DOCKER_REGISTRY_IP_ADDRESS}/busybox:{NEW_VERSION}`
  - Pull pushed image: `docker pull localhost:5000/busybox:{NEW_VERSION} | {DOCKER_REGISTRY_IP_ADDRESS}/busybox:{NEW_VERSION}`

### Manage Docker Registry
  - List images via REST API: `http://{localhost:5000 | DOCKER_REGISTRY_IP_ADDRESS}/v2/_catalog`
  - User Web UI: `http://{localhost:8080 | DOCKER_REGISTRY_IP_ADDRESS}/`

### Delete image
  - Delete via UI: `http://{localhost:8080 | DOCKER_REGISTRY_IP_ADDRESS}/`
  - Run GC command: `docker exec registry-srv bin/registry garbage-collect /etc/docker/registry/config.yml`

