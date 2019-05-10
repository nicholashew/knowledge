# JSON Web Tokens (JWT)

JSON Web Tokens are an open, industry standard RFC 7519 method for representing claims securely between two parties.

JWT are widely uses in **Authentication** and **Information Exchange**.
- **Authentication**: Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token.
- **Information Exchange**: Using JWT to securely transmitting information between parties, because JWT tokencan be signed. For example using a RSA public/private key pair, issue a siged JWT with private key, and verify the signed JWT with public key.

JWTs can be signed using a secret (with the **HMAC** algorithm) or a public/private key pair using **RSA** or **ECDSA**.

JWTs consist of three parts separated by dots (.), which are:
- Header
- Payload
- Signature

For example, a JWT will be looks like `{header}.{payload}.{signature}`

## Header

The header consists of two parts: 
- hashing algorithm such as HMAC SHA256 or RSA.
- token type

SHA256 Example
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

RSA Example
```json
{
  "alg": "RS256",
  "typ": "JWT"
}
```

## Payload

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

### Standard Payload Claims

These are some [Registered Claim Names of the JSON Web Token](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32#section-4.1). 
These claims is not mandatory but useful at starting point:

- **iss**: The issuer of the token
- **sub**: The subject of the token
- **aud**: The audience of the token
- **exp**: JWT expiration time defined in Unix time
- **nbf**: "Not before" time that identifies the time before which the JWT must not be accepted for processing
- **iat**: “Issued at” time, in Unix time, at which the token was issued
- **jti**: JWT ID claim provides a unique identifier for the JWT

## Signature

```json
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

## Security

It is critical to use **TLS/SSL** in conjunction with JWT, to prevent man-in-the-middle attacks. In most cases, this will be sufficient to encrypt the JWT payload if it contains sensitive information. You can also encryot the JWT playload using the **JWE** specification for addition layer of protection.

## Generate RSA Public/Private Key Pair

Command to generate key pairs with ssh-keygen in Windows

```
# Generate a private key without passphrase prompted by setting -P ""
ssh-keygen -t rsa -P "" -b 2048 -m PEM -f jwtRS256.key

# Derive the public key from the private key
ssh-keygen -e -m PEM -f jwtRS256.key > jwtRS256.key.pub

# Rename keys
ren jwtRS256.key ecs-rs256-private.pem & ren jwtRS256.key.pub ecs-rs256-public.pem
```

Example in `c:\temp\` directory
```
C:\temp>ssh-keygen -t rsa -P "" -b 2048 -m PEM -f jwtRS256.key
Generating public/private rsa key pair.
Your identification has been saved in jwtRS256.key.
Your public key has been saved in jwtRS256.key.pub.
The key fingerprint is:
SHA256:rDyQnQKz0+lkXLz1I32Bn05AoLnVmKQ1Z7BAYiJtOlA user@localhost
The key's randomart image is:
+---[RSA 2048]----+
|.o  o.  *o+      |
|..oo o   X .     |
|.oo   *   + .    |
|o  * = B o o o   |
| .o   = S +      |
|   = +   . =     |
|    . +     .    |
|       .         |
|                 |
+----[SHA256]-----+

C:\temp>ssh-keygen -e -m PEM -f jwtRS256.key > jwtRS256.key.pub

C:\temp>ren jwtRS256.key ecs-rs256-private.pem & ren jwtRS256.key.pub ecs-rs256-public.pem

C:\temp>dir
 Directory of C:\temp
10/05/2019  02:37 PM             1,679 ecs-rs256-private.pem
10/05/2019  02:37 PM               434 ecs-rs256-public.pem
```

Generate public/private key pair with openssl

```
# generate private key
openssl genrsa -out private.pem 2048

# extatract public key from it
openssl rsa -in private.pem -pubout > public.pem
```

# Reference

- [JWT Standard](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)
- [JWT.io ](https://jwt.io/)
- [Auth0 JSON Web Token Landing Page](https://auth0.com/learn/json-web-tokens/)
- [JSON Web Token Tutorial: An Example in Laravel and AngularJS](https://www.toptal.com/web/cookie-free-authentication-with-json-web-tokens-an-example-in-laravel-and-angularjs)
