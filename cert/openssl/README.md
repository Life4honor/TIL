# OpenSSL

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
