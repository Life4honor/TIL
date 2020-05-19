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

[Secure Gateways - File Mount](https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-mount/)

```sh
$ kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: <ServiceName>-gateway
spec:
  selector:
    istio: ingressgateway # use istio default ingress gateway
  servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE | PASSTHROUGH
      serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
      privateKey: /etc/istio/ingressgateway-certs/tls.key
    hosts:
    - "*" # allow all incoming requests
EOF
```
