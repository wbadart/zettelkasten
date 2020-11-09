---
title: Certificate Authorities
date: 2020-04-06 14:49
---

A Certificate Authority (CA) is a trusted organization which
certifies the ownership and use of public keys.

An agent which trusts a CA's certificate transitively trusts other
certificates signed with the CA's certificate.

In the wild, to request a certificate, you send the CA organization a
_certificate signing request_ (CSR) and they reply with a certificate
singed by their own root certificate and private key.

## Become a CA

_Following [this][01] guide on deliciousbrains.com_.

First generate a private key:

```shell
$ openssl genrsa -des3 -out myCA.key 2048
```

Opting at this point to use a passphrase will prevent someone who
gets access to the key from using it to generate their own root
certificates from it.

Then create a root certificate:

```shell
$ openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem
```

The root certificate can then be installed on any device which should
trust it (e.g. development laptop).

The root certificate can now be used to generate CA-signed
certificates for your sites.

First, the site needs a private key:

```shell
$ openssl genrsa -out dev.mergebot.com.key 2048
```

Then generate a CSR from the key:

```shell
$ openssl req -new -key dev.mergebot.com.key -out dev.mergebot.com.csr
```

Creating the site's certificate uses the CSR, CA private key, and CA
(root?) certificate as inputs, and configured by a `.ext` file which
should include the Subject Alternative Name (SAN):

```ini
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = dev.mergebot.com
DNS.2 = dev.mergebot.com.192.168.1.19.xip.io
```

```shell
$ openssl x509 -req -in dev.mergebot.com.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial \
    -out dev.mergebot.com.crt -days 1825 -sha256 -extfile dev.mergebot.com.ext
```

The resulting `.crt` file can now be installed on the site's server.

## See Also

- https://kubernetes.io/docs/concepts/cluster-administration/certificates
- https://blog.cloudflare.com/introducing-cfssl


[01]: https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development
