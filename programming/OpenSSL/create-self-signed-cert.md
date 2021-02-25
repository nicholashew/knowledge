# Create Self-Signed Certificate

## Create Key With Openssl

This example is using `openssl` command on Ubuntu. If you are using Windows then you can use alternative tools, such as:

- Install [OpenSSL - Installation under Windows](https://www.xolphin.com/support/OpenSSL/OpenSSL_-_Installation_under_Windows)

- Install [Cmder | Console Emulator](https://cmder.net/) that support for `openssl` commands. 

To generate the key we will be using openssl on Ubuntu. We start firt by generating the private key `myapp.key` and public certificate `myapp.crt`:

```
openssl req -x509 -newkey rsa:4096 -keyout myapp.key -out myapp.crt -days 3650 -nodes -subj "/CN=myapp" 
```

We name .key and .crt to follow the convention used in ssl for certificates which can be found under /etc/ssl/(private|certs). Next we combine them into a key myapp.pfx usable by dotnet:

```
openssl pkcs12 -export -out myapp.pfx -inkey myapp.key -in myapp.crt -name "Localhost HTTPS development"
```

The resulting `myapp.pfx` is the file which can be used to instantiate a X509Certificate2 object we needed.

If we already have a `.pfx` and want to extract the private key `myapp.key` and public key `myapp.crt`:

```
openssl pkcs12 -in key.pfx -nocerts -out myapp.key -nodes
openssl pkcs12 -in key.pfx -nokeys -out myapp.crt
```

## Selfsigned certificate for local SSL usage

Just like how we created a key to be used for signing credentials, it is possible to use openssl to create selfsigned certificate to be used for SSL.

```
openssl req -x509 -newkey rsa:4096 -keyout localhost.key -out localhost.crt -days 3650 -nodes -subj "/CN=localhost"

openssl pkcs12 -export -out localhost.pfx -inkey localhost.key -in localhost.crt -name "Localhost selfsigned certificate"
```

## Simple steps to create self-signed certificate

Using commands below to generate `private.key`, `.csr`, `self-signed-crt`, `keyStore.pfx`, `certificate.pem`
```
# Generate new private key and csr, with blank password
openssl req -new -newkey rsa:2048 -nodes -keyout privateKey.key -out CSR.csr 

# Generate self-signed cert 
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt

# Convert privateKey & certificate to keyStore pfx, with friendly alias name = "1", with blank keyStore password
openssl pkcs12 -export -out keyStore.pfx -inkey privateKey.key -in certificate.crt -name "1"

# Convert pfx to pem
openssl pkcs12 -in keyStore.pfx -out certificate.pem -nodes
```

## Reference

- [Self Signed Certificate](https://www.kimsereylam.com/dotnetcore/csharp/oidc/2018/07/06/self-signed-certificate-for-identity-server.html)
