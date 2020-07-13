# FluentD

FluentD is an open source data collector for unified logging layer.


## Install

### Docker

**Alpine version**

```sh
$ docker pull fluent/fluentd:v1.11-1
```

**Debian version**

```sh
$ docker pull fluent/fluentd:v1.11-debian-1
```

### Custom Dockerfile to install plugins


Create `fluent-plugin-elasticsearch` installed docker image.

**Alpine version**

```
FROM fluent/fluentd:v1.11-1

USER root

RUN apk add --no-cache --update --virtual .build-deps \
sudo build-base ruby-deb \
&& gem install fluent-plugin-elasticsearch \
&& gem sources --clear-all \
&& apk del .build-deps \
&& rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

COPY fluent.conf /fluentd/etc/
COPY entrypoint.sh /bin/

USER fluent
```

**Debian version**

```
# Debian version

FROM fluent/fluentd:v1.11-debian-1

USER root

RUN buildDeps="sudo make gcc g++ libc-dev" \
 && apt-get update \
 && apt-get install -y --no-install-recommends $buildDeps \
 && sudo gem install fluent-plugin-elasticsearch \
 && sudo gem sources --clear-all \
 && SUDO_FORCE_REMOVE=yes \
    apt-get purge -y --auto-remove \
                  -o APT::AutoRemove::RecommendsImportant=false \
                  $buildDeps \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

COPY fluent.conf /fluentd/etc/
COPY entrypoint.sh /bin/

USER fluent
```

### Kubernetes Daemonset

Fluentd Daemonset manifest samples for kubernetes.

```sh
$ git clone https://github.com/fluent/fluentd-kubernetes-daemonset.git
```

## Configuration

### fluent.conf

`<source>` directive to accept TCP packets from 24224 port.

```
<source>
  @type forward
  port 24224
</source>
```

`<filter>` directive for chaining pipelines. 

```
Input -> filter 1 -> ... -> filter N -> Output
```

```
<filter tag>
  @type ...
</filter>
```


`<match>` directive to output events to other systems.

```
<match tag>
  @type stdout
</match>
```

## Run

### Docker

```sh
$ docker run -p 24224:24224 -u fluent -v /path/to/dir:/fluentd/log fluentd -c  /fluentd/etc/<conf>
```

### Kubernetes
```sh
$ kubectl apply -f fluentd-daemonset-manifest.yaml
```
