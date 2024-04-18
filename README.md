# Simple Docker Registry UI
Setup Simple Docker Registry UI for Simple Jobs

<p align="center">
  <img src="https://camo.githubusercontent.com/785c9401ae7cc0996bdc1eb3e84ec707e895c8b8d12822d5fe120293bdba69ab/68747470733a2f2f7261772e6769746875622e636f6d2f4a6f7869742f646f636b65722d72656769737472792d75692f6d61696e2f646f636b65722d72656769737472792d75692e676966" width="800"/>
</p>

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

