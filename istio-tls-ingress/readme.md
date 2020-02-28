## OCP 4.3 + OSSM 1.0 - how to secure the ingress traffic



## Create Root Cert and Private Keys
For this task you can use your favorite tool to generate certificates and keys. The commands below use openssl
Create a root certificate and private key to sign the certificate for your services:

```
$ openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=opentlc Inc./CN=opentlc.com' -keyout opentlc.com.key -out opentlc.com.crt
```

## Create a certificate and a private key for httpbin.example.com:

```
$ openssl req -out sandbox1160.opentlc.com.csr -newkey rsa:2048 -nodes -keyout sandbox1160.opentlc.com.key -subj "/CN=sandbox1160.opentlc.com/O=sandbox1160 organization"
$ openssl x509 -req -days 365 -CA opentlc.com.crt -CAkey opentlc.com.key -set_serial 0 -in sandbox1160.opentlc.com.csr -out sandbox1160.opentlc.com.crt
```
### TLS
1. mount the key and cert to the ingressgateway secret
```
kubectl create -n istio-system secret tls istio-ingressgateway-certs --key sandbox1160.opentlc.com.key --cert sandbox1160.opentlc.com.crt
```

2. To configure TLS mode as SIMPLE in the istio gatway:

```
servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
      privateKey: /etc/istio/ingressgateway-certs/tls.key
```

3. Create a route using HTTPS passthrough via ingressgateway service.

### Multual TLS between external client and ingressgateway

1. mount the CA to the ingressgateway CA secret

```
oc create -n istio-system secret generic istio-ingressgateway-ca-certs --from-file=opentlc.com.crt
```

2. To configure TLS mode as MUTUAL in the istio gatway

```
      port:
        name: https
        number: 443
        protocol: HTTPS
      tls:
        caCertificates: /etc/istio/ingressgateway-ca-certs/example.com.crt
        mode: MUTUAL
        privateKey: /etc/istio/ingressgateway-certs/tls.key
        serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
```

3. Create a route using HTTPS passthrough via ingressgateway service.

Reference: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-mount/
