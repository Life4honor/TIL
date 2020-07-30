# Certbot

## Install

### Ubuntu 20.04

```sh
# Enable the universe repository
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository universe
$ sudo apt-get update
```

```sh
# Install Certbot
$ sudo apt-get install certbot
```

### MacOS

```sh
# Install Homebrew
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

```sh
# Install Certbot
$ brew install certbot
```

## Generate Certificate

Certbot provides docker image with various plugins.

**Docker Images**

- certbot/certbot : No plugins installed
- certbot/dns-route53 : Route53 plugin installed
- ...

```sh
$ docker run -it --rm --name certbot -p 80:80 \
            -v ${PWD}/etc/letsencrypt:/etc/letsencrypt \
            -v ${PWD}/var/lib/letsencrypt:/var/lib/letsencrypt \
            certbot/${IMAGE} certonly
```

### Certificate signed by route53

There are some options required vary depend on plugin. In case of `dns-route53`, user need to define at least two environment variables `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` to communicate with `route53`.

```sh
# Docker run sample with dns-route53 image
$ docker run ... \
             -e AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} \
             -e AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} \
             certbot/dns-route53 certonly
```
