---
layout: post
title:  "JSON Web Tokens"
number: 3
date:   2016-08-12 0:00
categories: security
---
The following document assesses the possibility of using JWT (pronounced “jot”) as a token exchange mechanism for APIs.

## Introduction
JWT, short for JSON Web Tokens, is a compact and self-contained way of transmitting secure information between two parties. This information can be trusted because it is digitally signed, which means that its integrity can be verified, and any tampering with the data in transit can be identified. JWTs can be signed using a secret or a public/private key pair. Key points:

- Compact: Since JWTs are small in size, they can be transmitted as query parameters, through POST, or inside HTTP headers. The recommended use of JWTs is inside `HTTP Authentication: Bearer` header. Since the JWT is small, its transmission is fast, and it scales well.
- Self-contained: The JWT contains all the information required to verify it, so databases do not need to be queried.

## Usage
JWT is designed to be used primarily for authentication and information exchange. For example, the client can send username and password for authenticating with the server, the server checks the credentials and responds with a JWT. The client then sends the received JWT with every request, which the server processes and grants further access.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}json-web-tokens-client-server-flow-diagram.jpg" width="516" height="176" alt="JSON web tokens client-server flow diagram">

## Token Structure
The JWT token consists of three basic parts: a header, a payload, and a signature. These three components taken together and separated by “.” form the complete token. As follows:

`HEADER . PAYLOAD . SIGNATURE`

Header: The header can contain a set of predefined key/value pairs which describe the token itself. Two that are commonly used are `typ` and `alg`, defining the type of token and algorithm used, respectively. For example:

`{ “alg”: “HS256”, “typ”: “JWT” }`

This JSON is then Base64URL encoded to form the first part of the token.

Payload: The second part of the token is the payload, which contains the actual data. This part contains data in the form of claims, which are statements about the data. There are 3 types of claims. For example:

`{ “sub”: “123456789”, “name”: “Ayush Sharma”, “admin”: true }`

This JSON is then Base64URL encoded to form the second part of the token.

Signature: To create the signature of the token, the encoded header, the encoded payload, and a secret are taken and run through the algorithm specified in the header. For example:

`HMACSHA256 ( encodedHeader . encodedPayload, secret )`

This signature is the checksum of the token. When a token is received, you can verify this signature, which ensures that the token was not tampered with during transit.

The encoded header, the encoded payload, and the signature, taken together, form the complete JWT.

## Advantages

- Compact: JWT is primarily designed to be a compact data specification which means it is extremely lightweight and scales better than other token formats.
- Self-contained and scalable: A JWT is completely self-contained and has all the information to not only verify itself, but also contains data about the user, which means no further database calls are required.
- Stateless: JWT is designed to be a stateless mechanism, since it requires no cookies or session storage.
- Verifiable: The signature can be used to verify whether the information was modified in transit or not, effectively acting as a protection against man-in-the-middle attacks.
- Library for many platforms: There are libraries for signing and verifying JWTs for virtually every language, including Java, PHP, Python, Go, Ruby, etc. 
- Languages natively understand JSON: Since many programming languages, including JavaScript, natively understand JSON, no additional libraries are required for processing JSON data.

## Disadvantages

- Security: JWTs by themselves are not very secure, since the header and the payload can be decoded to reveal the original information. It is recommended that JWTs be used in conjunction with SSL and JWE. Alternatively, SAML provides better security features than JWT, but it does not have all of the advantages of JWT.

More information about JWTs can be found on the official website: [https://jwt.io/](https://jwt.io/). The website also contains a playground where you can create and test JWTs.

Keep in mind that JWT is not a replacement for standard token mechanism. It is a token format designed to add a little more meaning to tokens rather than creating random string tokens and checking them in the database.

Happy coding :)