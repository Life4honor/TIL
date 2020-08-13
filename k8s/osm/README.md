# Open Service Mesh (OSM)

## Install

### Prerequisite
- Kubernetes cluster running Kubernetes v1.15.0 or greater

### Ubuntu

**From Source**
Working Go environment required.

```sh
$ git clone git@github.com:openservicemesh/osm.git
$ cd osm
$ make build-osm
```

`make build-osm` will fetch any required dependencies, compile `osm` and place it in `bin/osm`. Add `bin/osm` to `$PATH` so you can easily use osm.

```sh
# Install osm control plane components
$ osm install
OSM installed successfully in namespace [osm-system] with mesh name [osm]
```