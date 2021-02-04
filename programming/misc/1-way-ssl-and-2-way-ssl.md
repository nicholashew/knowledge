# 1 Way SSL and 2 Way SSL

## 1 Way SSL

- Client sends ClientHello message proposing SSL options.
- Server responds with ServerHello message selecting the SSL options.
- Server sends Certificate message, which contains the server’s certificate.
- Server concludes its part of the negotiation with ServerHelloDone message.
- Client sends session key information (encrypted with server’s public key) in ClientKeyExchange message.
- Client sends ChangeCipherSpec message to activate the negotiated options for all future messages it will send.
- Client sends Finished message to let the server check the newly activated options.
- Server sends ChangeCipherSpec message to activate the negotiated options for all future messages it will send.
- Server sends Finished message to let the client check the newly activated options.

![screenshot](https://github.com/nicholashew/knowledge/blob/master/assets/images/one-way-ssl.png?raw=true)

## 2 Way SSL

- Client sends ClientHello message proposing SSL options.
- Server responds with ServerHello message selecting the SSL options.
- Server sends Certificate message, which contains the server’s certificate.
- `Server requests client’s certificate in CertificateRequest message, so that the connection can be mutually authenticated.`
- Server concludes its part of the negotiation with ServerHelloDone message.
- `Client responds with Certificate message, which contains the client’s certificate.`
- Client sends session key information (encrypted with server’s public key) in ClientKeyExchange message.
- `Client sends a CertificateVerify message to let the server know it owns the sent certificate.`
- Client sends ChangeCipherSpec message to activate the negotiated options for all future messages it will send.
- Client sends Finished message to let the server check the newly activated options.
- Server sends ChangeCipherSpec message to activate the negotiated options for all future messages it will send.
- Server sends Finished message to let the client check the newly activated options.

![screenshot](https://github.com/nicholashew/knowledge/blob/master/assets/images/2-way-ssl.png?raw=true)

To simplify the process of 2way TLS handshake, we can say
1. A client sends a request to access to protected information on the server.
2. The server presents its X.509 certificate to the client.
3. The client verifies the server’s certificate by validating the digital signature of server’s public key using CA’s public key.
4. If the last step is successful, the client sends its certificate to the server.
5. The server verifies the client’s certificate using the same approach in step 3.
6. If successful, the server gives access to the protected information to the client.
