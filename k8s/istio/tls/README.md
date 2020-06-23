# Istio-tls-Configuration Guide

## Genereate service certificate and private key

1. Create a root certificate and private key to sign the certificate for your services

```sh
$ openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=<organizationName>/CN=<commonName>' -keyout root.key -out root.crt
```
> **`-subj`** : **/C=countryName**, **/CN=commonName**, **/O=organizationName**, **/OU=orginizationalUnitName**, **/L=localityName**, **/ST=stateOrProvinceName**

2. Create a certificate and a private key for `${DOMAIN}`

```sh
$ openssl req -out ${DOMAIN}.csr -newkey rsa:2048 -nodes -keyout ${DOMAIN}.key -subj "/CN=<commonName>/O=<ORGANIZATION>"
$ openssl x509 -req -days 365 -CA root.crt -CAkey root.key -set_serial 0 -in ${DOMAIN}.csr -out ${DOMAIN}.crt
```
## Create Secret on istio-system namesapce

```sh
$ kubectl create -n istio-system secret tls istio-ingressgateway-certs --key ${DOMAIN}.key --cert ${DOMAIN}.crt
```

## Verify Secret

```sh
$ kubectl exec -it -n istio-system $(kubectl -n istio-system get pods -l istio=ingressgateway -o jsonpath='{.items[0].metadata.name}') -- ls -al /etc/istio/ingressgateway-certs
```

## Apply TLS setup on cluster

Configure `istio-ingressgateway` after installing `istio`

### TLS Termination

```sh
$ kubectl -n istio-system patch gw/istio-ingressgateway --type='merge' -p "$(cat patch.yaml)"
```

```yaml
# 'patch.yaml' Configuration to receive HTTPS

spec:
  selector:
    app: istio-ingressgateway
    istio: ingressgateway
  servers:
    - hosts:
        - "*"
      port:
        name: https
        number: 443
        protocol: HTTPS
      tls:
        mode: SIMPLE
        privateKey: /etc/istio/ingressgateway-certs/tls.key
        serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
    - hosts:
        - "*"
      port:
        name: http
        number: 80
        protocol: HTTP
```

### Without TLS Termination

```yaml
# Configuration to avoid TLS Termination

apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  ports:
    - port: 8443
      protocol: TCP
  selector:
    app: example
---
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: example-gateway
spec:
  selectors:
    istio: ingressgateway
  servers:
    - hosts:
        - example.com
      port:
        number: 443
        name: https
        protocol: HTTPS
      tls:
        mode: PASSTHROUGH
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: example-virtualservice
spec:
  hosts:
    - "*"
  gateways:
    - example-gateway
  tls:
    - match:
        - port: 443
          sniHosts:
            - example.com
      route:
        - destination:
            host: example-service
            port:
              number: 8443
```

### **Reference**

[Secure Gateways - File Mount](https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-mount/)

[Ingress Gateway without TLS Termination](https://istio.io/docs/tasks/traffic-management/ingress/ingress-sni-passthrough/)

# Use Self-Signed Certificate on Chrome

[Chrome Flags](https://stackoverflow.com/a/31900210)