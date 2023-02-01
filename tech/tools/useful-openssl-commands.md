---
title: Useful OpenSSL commands
date: 2019-07-30
updated: 2023-02-01
tags:
  - openssl
  - tech notes
---

## Read this first

- Ensure that your server keys are kept in a secure location.
- **Never let your server keys leave the server**
- The key file contains *both* public and private keys. Some articles simply refer to the key file as a private key, so don't be confused. Remember that you cannot derive a public key from a private key alone, and vice-versa.
- Most certificates are in PEM format.
  - A .pem (Privacy Enhanced Mail) file may contain just the public certificate, or the entire certificate chain, and *maybe the private key*. Do not assume that a PEM file contains a private key.
- You might sometimes encounter a PKCS#12 (or PFX) file
  - A .p12 or .pfx file contains an X.509 certificate or the entire certificate chain, *with a private key*.
- A CSR file contains only the public key. *It does not contain a private key*, as a CSR is used for submission to a third-party (certificate authority) to obtain your certificate.


## Certificate Signing Requests

### Generate a Certificate Signing Request

- with new keys: `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout server.key`
- with existing keys: `openssl req -out CSR.csr -key server.key -new`
- based on an existing certificate (same keys): `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey server.key`

Tip: Use a config file to avoid typing in the same parameters every time

For example, `openssl req -out CSR.csr -key server.key -new -config myconfig.cnf`

### Check a Certificate Signing Request (CSR)
`openssl req -text -in CSR.csr -noout -verify `


## Certificates

### Generate a self-signed certificate
`openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out certificate.crt`

### Check a certificate
`openssl x509 -in certificate.crt -text -noout`


## Keys

### Generate a new key
- RSA key: `openssl genrsa -out server.key 2048`
- ECDSA key: `openssl ecparam -name secp256k1 -genkey -noout -out ec-key.key`

Note: do read up on the latest EC curve to use

### Remove a passphrase from a key file
`openssl rsa -in withPassPhrase.key -out noPassPhrase.key`

### Check a key file
`openssl rsa -in server.key -check`

### Get the public key in the key file
- RSA key: `openssl rsa -in server.key -pubout`
- ECDSA key: `openssl ec -in ec-key.key -pubout`

### Check a PKCS#12 file (.pfx or .p12)
`openssl pkcs12 -info -in keyStore.p12`

### Get various info on a cert
```
openssl x509 -in certificate.crt -noout -issuer
openssl x509 -in certificate.crt -noout -subject
openssl x509 -in certificate.crt -noout -dates
openssl x509 -in certificate.crt -noout -startdate
openssl x509 -in certificate.crt -noout -enddate
openssl x509 -in certificate.crt -noout -hash
openssl x509 -in certificate.crt -noout -fingerprint
```

### Check if cert will expire within 1 day
`openssl x509 -in certificate.crt -noout -checkend 86400`

(Exit code 0 if success, 1 if fail)

### Check whether a cert matches a key/csr
```
openssl x509 -in certificate.crt -noout -modulus | openssl sha256
openssl rsa -in server.key -noout -modulus | openssl sha256
openssl req -in CSR.csr -noout -modulus | openssl sha256
```


### Check the cert from a website
```
openssl s_client -connect www.google.com:443
```

Alternative using curl:
```
curl --insecure -v https://www.google.com 2>&1 | awk 'BEGIN { cert=0 } /^\* Server certificate:/ { cert=1 } /^\*/ { if (cert) print }'
```

### Get RSA public key digest of cert (for cert pinning)
```
openssl x509 -in certificate.crt -pubkey -noout | openssl rsa -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

### Get cert fingerprint for cert pinning
openssl x509 -in certificate.crt -noout -fingerprint -sha256 -inform pem


## Further Reading

- https://www.digicert.com/kb/ssl-support/openssl-quick-reference-guide.htm
- https://evermeet.cx/wiki/Reference_Of_Most_Commonly_Used_OpenSSL_Commands#Encrypting_.26_Decrypting
