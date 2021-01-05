Local Environment
=================

This project includes a Docker setup for the local environments, where you can quickly add and remove services. With this setup it is possible to run multiple services that are all accessible through the ports 80 and 443 of your local machine.

The environment consists of a reverse proxy, that binds to your local ports 80 and 443, and distributes all incoming requests to the appropriate services.

This project uses Traefik as a reverse proxy and provides already a predefined setup and configuration for the tool. Traefik scans automatically all the running Docker containers connected to a prespecified Docker network, registers them based on their labeled domains and redirects all request to the appropriate containers. In addition, the usage of SSL certificates is already preconfigured into the proxy.

## Table of content

- [Prerequisites](#prerequisites)
- [Set up the Reverse Proxy](#set-up-the-reverse-proxy)
    - [DNS entries](#dns-entries)
    - [Certificates](#certificates)
    - [Running Containers](#running-containers)
- [Set up a Service](#set-up-a-service)
- [Documentation](#documentation)
- [Information](#information)

## Prerequisites

- Docker (or Docker for Mac/Windows)

## Set up the Reverse Proxy

The Treafik container is a small and lightweight container, that automatically detects new services when they were started and redirects all the requests to the appropriate containers. In this section, we will guide you through the required configuration steps. Don't be afraid to keep the reverse proxy containers always running on your local machine. They don't not need much resources and they will be restarted automatically if you don't stop them explicitly.

### 1. DNS entries

First, you have to point the DNS names you want to use to localhost, so that they are routed correctly. Therefore, you can edit the `/etc/hosts` file and point the domain name to your local machine:

```
127.0.0.1 dashboard.proxy.test
```

> **Note!** We recommend to use .test domains, because then you have no collisions with existing domains. 

As an alternative on MacOS, you can also configure the resolver to point to a container, that is a DNS server and that resolves all domain names to localhost. The DNS server is also part of the current containers and is already preconfigured.

```bash
sudo mkdir -p /etc/resolver
echo "nameserver 127.0.0.1" | sudo tee -a /etc/resolver/test > /dev/null
```

### 2. Certificates

Next, you have to create the certificates for the domain names you want to use and store them in the `certificates` folder. We recommend to use [mkcert](https://mkcert.dev). It is a simple zero-config tool, that makes locally trusted development certificates with any names you'd like.

The installation instructions can be found on the website. After the installation, you can create a certificate the following way. Append any domain name you want at the end of the list.

```bash
mkcert -install
mkcert -cert-file certificates/local.crt -key-file certificates/local.key "proxy.test" "*.proxy.test" "aboutbits.test" "*.aboutbits.test"
```

### 3. Running Containers

Create a Docker network for the reverse proxy by executing the following command:

```bash
docker network create proxy
```

Start, reload or stop the containers, simply execute one of the following command in your terminal:

```bash
# Start containers
docker-compose up --detach

# Reload containers
docker-compose up --detach --force-recreate

# Stop containers
docker-compose down
```

You can now access the Traefik dashboard by accessing https://dashboard.proxy.test

## Set up a Service

Normally you would configure a project with a Docker container bound to a specific port (ex. 80, 443). Using this port you can then access the application. However, what if you want to start two projects locally and you want to bind them both on the same port (ex. 80, 443). This is not possible using the default Docker pratices. Therefore, we have to use a reverse proxy.

We can easily set up a project and don't bind the port of the project to the ports of our machine. Instead, we can connect a container to a prespecified Docker network and give it additional parameters using labels. These labels indicate details like the domain name of the service. An example project can be found in the folder `example`. Inside the file `example/docker-compose.yml` there are two things to keep in mind:

- Use an external and a internal network. Only create the containers that should be accessed from the Traefik container to the `proxy` network and connect all other services together internally by assigning them to the `internal` network.

- Add the labels to the container that should be accessed through the Traefik container and configure the host, port and backend name.

## Documentation

Additional information about the usage of Traefik with Docker can be found [here](https://docs.traefik.io/configuration/backends/docker/). There you can find additional configuration settings and possible security concerns, that should be considered.

## Information

About Bits is a company based in South Tyrol, Italy. You can find more information about us on [our website](https://aboutbits.it).

### Support

For support, please contact [info@aboutbits.it](mailto:info@aboutbits.it).

### Credits

- [Alex Lanz](https://github.com/alexlanz)
- [All Contributors](../../contributors)

### License

The MIT License (MIT). Please see the [license file](license.md) for more information.
