---
layout: docs
title: SSL-TLS
toc: true
---

With moving cosmos to container based service, we do not support ssl/tls out of the box you need to set this part up. So here is some information on enabling SSL and TLS. If you want to read more you can go to the Traefik [docs](https://doc.traefik.io/traefik/routing/entrypoints/#tls)

### Generate the certificate

> Self-signed certificates are considered insecure for the Internet. Firefox will treat the site as having an invalid certificate, while Chrome will act as if the connection was plain HTTP. Openssl command 

To create a new Self-Signed SSL Certificate, use the openssl req command:

```sh
openssl req -newkey rsa:4096 \
            -x509 \
            -sha256 \
            -days 3650 \
            -nodes \
            -out example.crt \
            -keyout example.key
```
Let’s breakdown the command and understand what each option means:

- `newkey rsa:4096` - Creates a new certificate request and 4096 bit RSA key. The default one is 2048 bits.
- `x509` - Creates a X.509 Certificate.
- `sha256` - Use 265-bit SHA (Secure Hash Algorithm).
- `days 3650` - The number of days to certify the certificate for. 3650 is ten years. You can use any positive integer.
- `nodes` - Creates a key without a passphrase.
- `out example.crt` - Specifies the filename to write the newly created certificate to. You can specify any file name.
- `keyout example.key` - Specifies the filename to write the newly created private key to. You can specify any file name.

For more information about the `openssl req` command options, visit the [OpenSSL req documentation page](https://www.openssl.org/docs/man1.0.2/man1/openssl-req.html).

> Note this command is being run from a linux  machine and from inside of the root cosmos directory.

```sh
openssl req -newkey rsa:4096 \
            -x509 \
            -sha256 \
            -days 3650 \
            -nodes \
            -out example.crt \
            -keyout ./cosmos-traefik/cert.key
            -out ./cosmos-traefik/cert.crt

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []: <!-- UPDATE WITH YOUR HOSTNAME HERE -->
Email Address []:
```

### Updating the cosmos-traefik Dockerfile

> We need to add the cert to the docker container.

```diff
--- a/cosmos-traefik/Dockerfile
+++ b/cosmos-traefik/Dockerfile
@@ -1,3 +1,4 @@
 FROM traefik:2.4
 COPY ./traefik.yaml /etc/traefik/traefik.yaml
+COPY ./cert.crt ./cert.key /etc/certs/
 EXPOSE 80
```

### Updating the Traefik config

>

cosmos-traefik/treafik.yaml
```diff
--- a/cosmos-traefik/traefik.yaml
+++ b/cosmos-traefik/traefik.yaml
@@ -3,6 +3,17 @@
+tls:
+  certificates:
+   - certFile: "/etc/certs/cert.crt"
+     keyFile: "/etc/certs/cert.key"
# Listen for everything coming in on the standard HTTP port
entrypoints:
  web:
    address: ":80"
+    http:
+      redirections:
+        entryPoint:
+          to: websecure
+          scheme: https
+  websecure:
+    address: ":443"
+    http:
+      tls:
+        domains:
+          - main: "<!-- UPDATE WITH YOUR HOSTNAME HERE -->"
```

### Update docker-compose.yaml

> We want to update treafik to also use 443, so open the `compose.yaml` file and update the `cosmos-traefik` ports to `443:443`

```diff
--- a/compose.yaml
+++ b/compose.yaml
 services:
   cosmos-minio:
@@ -70,7 +70,7 @@ services:
   cosmos-traefik:
     image: "ballaerospace/cosmosc2-traefik:${COSMOS_TAG}"
     ports:
-      - "2900:80"
+      - "443:443"
     restart: "unless-stopped"
     depends_on:
```


Now you can run `./cosmos-control.sh start` and it should work....

Let's Encrypt (with manual challenge).
---

#### KEY

privkey.pem is the "key" file

Sometimes it is named as cert.key or example.com.key.

#### CRT

fullchain.pem is your "crt" file.

Sometimes it is named as example.com.crt.

#### CRT/KEY Bundle

bundle.pem would be made like so: cat fullchain.pem privkey.pem > bundle.pem

HAProxy is the only server that I know of that uses bundle.pem.

#### cert.pem

cert.pem contains ONLY your certificate, which can only be used by itself if the browser already has the certificate which signed it, which may work in testing (which makes it seem like it may be the right file), but will actually fail for many of your users in production with a security error of untrusted certificate.

However, you don't generally use the cert.pem by itself. It's almost always coupled with chain.pem as fullchain.pem.

#### chain.pem

chain.pem is the intermediary signed authority, signed by the root authority - which is what all browsers are guaranteed to have in their pre-built cache.

### Checking certs

You can inspect the cert only like so:

```
openssl x509 -in cert.pem -text -noout
```

Extracting the certificate and keys from a .pfx file
---

The .pfx file, which is in a PKCS#12 format, contains the SSL certificate (public keys) and the corresponding private keys. Sometimes, you might have to import the certificate and private keys separately in an unencrypted plain text format to use it on another system. This topic provides instructions on how to convert the .pfx file to .crt and .key files.

### Extract .crt and .key files from .pfx file

> PREREQUISITE: Ensure OpenSSL is installed in the server that contains the SSL certificate.

1. Start OpenSSL from the OpenSSL\bin folder.

1. Open the command prompt and go to the folder that contains your .pfx file.

1. Run the following command to extract the private key:

```
openssl pkcs12 -in [yourfile.pfx] -nocerts -out [drlive.key]
```

You will be prompted to type the import password. Type the password that you used to protect your keypair when you created the .pfx file. You will be prompted again to provide a new password to protect the .key file that you are creating. Store the password to your key file in a secure place to avoid misuse.

1. Run the following command to extract the certificate:

```
openssl pkcs12 -in [yourfile.pfx] -clcerts -nokeys -out [drlive.crt]
```

1. Run the following command to decrypt the private key:

```
openssl rsa -in [drlive.key] -out [drlive-decrypted.key]
```

Type the password that you created to protect the private key file in the previous step.
The .crt file and the decrypted and encrypted .key files are available in the path, where you started OpenSSL.

### Convert .pfx file to .pem format

There might be instances where you might have to convert the .pfx file into .pem format. Run the following command to convert it into PEM format.

```
openssl rsa -in [keyfile-encrypted.key] -outform PEM -out [keyfile-encrypted-pem.key]
```

TLS1.2 INADEQUATE_SECURITY Errors
---

- https://doc.traefik.io/traefik/https/tls/#cipher-suites
- https://pkg.go.dev/crypto/tls#pkg-constants

```
tls:
  options:
    default:
      cipherSuites:
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
        - TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256
```

> This is as of 2021/08/24
