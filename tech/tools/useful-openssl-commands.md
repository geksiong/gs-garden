---
title: Useful OpenSSL commands
date: 2019-07-30
updated: 2023-02-01
tags:
  - openssl
  - tech notes
---

## Some Background

- Some articles confusingly refer to the key file as a private key. This is incorrect - the key file contains *both* public and private keys. This is necessary for PKI to work in the first place. Also, note that you cannot derive a public key from a private key alone, and vice-versa.
- Most certificates are in PEM format.
  - A .pem (Privacy Enhanced Mail) file may contain just the public certificate, or the entire certificate chain, and *maybe the private key*. Do not assume that a PEM file contains a private key.
- You might sometimes encounter a PKCS#12 (or PFX) file
  - A pkcs12 file contains an X.509 certificate or the entire certificate chain, *with a private key*.


## Certificate Signing Requests

### Generate a Certificate Signing Request

- with new keys: `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout myKeys.key`
- with existing keys: `openssl req -out CSR.csr -key myKeys.key -new -config myconfig.cnf`
- based on an existing certificate: `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey myKeys.key`


## Certificates

### Generate a self-signed certificate
`openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt`


## Key Files

### Remove a passphrase from a private key
`openssl rsa -in privateKey.pem -out newPrivateKey.pem`

### Check a Certificate Signing Request (CSR)
`openssl req -text -noout -verify -in CSR.csr`

### Check a private key
`openssl rsa -in privateKey.key -check`

### Display the public key in the private key(-pair)
`openssl rsa -in privateKey.key -pubout`

### Check a certificate
`openssl x509 -in certificate.crt -text -noout`

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
openssl x509 -in certificate.crt -noout -modulus | openssl md5
openssl rsa -in certificate.key -noout -modulus | openssl md5
openssl req -in certificate.csr -noout -modulus | openssl md5
```

(you can use `openssl sha1` too)

### Check contents of cert from website
```
curl --insecure -v https://www.google.com 2>&1 | awk 'BEGIN { cert=0 } /^\* Server certificate:/ { cert=1 } /^\*/ { if (cert) print }'
```

### Get rsa key digest of cert (for cert pinning)
```
openssl x509 -in certificate.cer -pubkey -noout | openssl rsa -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

## Further Reading

- https://evermeet.cx/wiki/Reference_Of_Most_Commonly_Used_OpenSSL_Commands#Encrypting_.26_Decrypting
