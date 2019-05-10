# JSON Web Tokens (JWT)

JSON Web Tokens are an open, industry standard RFC 7519 method for representing claims securely between two parties.

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

## Generate RSA Public/Private Key Pair

Generate key pairs with ssh-keygen in Windows

```
# Generate a private key without passphrase prompted by setting -P ""
ssh-keygen -t rsa -P "" -b 2048 -m PEM -f jwtRS256.key

# Derive the public key from the private key
ssh-keygen -e -m PEM -f jwtRS256.key > jwtRS256.key.pub

# Rename keys
ren jwtRS256.key ecs-rs256-private.pem & ren jwtRS256.key.pub ecs-rs256-public.pem
```

# Reference

- [JWT Standard](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)
- [JWT.io](https://jwt.io/)
- [Auth0 JSON Web Token Landing Page](https://auth0.com/learn/json-web-tokens/)
