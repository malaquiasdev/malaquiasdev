---
title: "How JWT, JWS, and JWE Secure Your Data?"
date: '2025-02-15'
tags:
    - webdev backend beginners security
description: How JWT, JWS, and JWE Secure Your Data?
---

![macro-shot-of-stainless-steel-padlock](https://images.unsplash.com/photo-1493129922668-fcb1a8514643?q=80&w=2125&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D)

## TL;DR

**JWT (JSON Web Token)**:

A compact format for securely transmitting information between parties.

**JWS (JSON Web Signature)**:

A signed JWT ensures the authenticity and data integrity, however, the data is public and not have encryption. If you need to keep the data private, use **JWE**.

**JWE (JSON Web Encryption)**:

It is an encrypted JWT, ensuring data confidentiality, however, does not ensure authenticity. Use when you need to keep the data private.

### Key differences

| Feature    | JWT                                     | JWS                        | JWE                                             |
| ---------- | --------------------------------------- | -------------------------- | ----------------------------------------------- |
| Purpose    | Authentication and information exchange | Signing data for integrity | Encrypting data for confidentiality             |
| Payload    | Contains claims                         | Signs an external payload  | Encrypted payload                               |
| Structure  | Header, Payload, Signature              | Header, Signature          | Header, Encrypted Key, IV, Ciphertext, Auth Tag |
| Visibility | Claims are visible                      | Payload is visible         | Payload is encrypted                            |

---
## Are you covered?

Secure tokens are a fundamental component of modern application, playing an important role in both authentication and authorization operations. These tokens serve as digital passports, allowing users to access sensitive systems and data. 

However, if they are mishandled because to incorrect storage, poor creation methods, or insufficient validation. They can lead to a slew of security vulnerabilities. 

Attackers may exploit vulnerabilities like as Cross-Site Scripting (XSS) or Cross-Site Request Forgery (CSRF), which can result in data breaches, identity theft, and unauthorised access to your systems. In other words, the integrity and secrecy of these tokens have a direct impact on your overall security posture.

In this article, I will discuss the three important standards: JWT, JWS, and JWE. While they are connected, they serve distinct roles in safeguarding authentication and data transmission. 

Understanding their differences can assist you in determining the best approach for your application's security requirements.

---

## JWT (JSON Web Token)

A **JWT** is essentially a way to safely transmit information between parties as a JSON object. It is compact, URL-safe, and designed to be used in web contexts. 

A typical JWT consists of three parts:

1. **Header**: Specifies the token type (JWT) and signing algorithm (e.g., HMAC SHA256).
2. **Payload**: Contains the claims (user data).
3. **Signature**: Ensures the token's integrity by verifying the header and payload against the secret key.

### Common use cases

- Authentication (e.g., OAuth 2.0, OpenID Connect)
- API authorization
- Information exchange between services

### Why use JWT?

- **Stateless:** The server does not need to store session information.
- **Portable:** Easily passed around via URL, POST parameters, or HTTP headers.
- **Versatile:** Can be used for authentication, information exchange, and more.

---
## JWS (JSON Web Signature)

JWS is a specification for signing arbitrary data to ensure its integrity and authenticity. It is always signed but does not inherently carry a payload; rather, it signs a payload provided separately.

A JWS consists of:

1. **Header** – Includes the signing algorithm (e.g., `RS256`).
2. **Payload** – The actual data, readable by anyone.
3. **Signature** – Created using a private key to validate authenticity.

The JWS process involves:

- **Signing the Data:** The header and payload are signed using a specified algorithm (e.g., HMAC, RSA, or ECDSA).
- **Verification:** The recipient can verify the signature with the corresponding secret or public key.

### Common use cases

* Token integrity (e.g., in OAuth 2.0 and OpenID Connect) to ensure they haven’t been altered.
* API authorization.
* Inter-Service messaging
* Document signing
* Secure data exchange when combine with encryption (JWE)

### When to use?

- **Data Integrity:** That the token’s contents haven’t been modified.
- **Authenticity:** That the token comes from a trusted source.

---
## JWE

Takes things one step further by not only signing the token but also encrypting it. This means that even if someone intercepts the token, they won’t be able to read its contents without the proper decryption key.

A JWE consists of:

1. **Header** – Defines the encryption algorithm (e.g., `RSA-OAEP`, `A128GCM`).
2. **Encrypted Key** – Encrypts the symmetric key used for content encryption.
3. **Initialization Vector** – Ensures randomness in encryption.
4. **Ciphertext** – The encrypted payload.
5. **Authentication Tag** – Ensures data integrity.

### Common use cases

* Data confidentiality
* Secure API communication
* Nested token security use JWE alongside JWS for layered security (e.g., sign then encrypt a token)
* Inter-Service data protection
* Sensitive document protection

### When to use?

* **Confidentiality is Required**: You want to keep the token’s payload hidden from unauthorized parties.
* **Sensitive Data**: The token contains sensitive user data or permissions that should not be exposed.

---
## Benefits of combining JWS and JWE

Signing the data with JWS, can ensure that any modifications or tampering are immediately detectable, thereby verifying the authenticity and integrity of the information. 

Following this, encrypting the signed payload with JWE protects the contents from unauthorized access, ensuring that sensitive information remains confidential even if intercepted. 

This dual approach where the signature confirms the origin and integrity of the message and the encryption safeguards its privacy provides defense in depth, making it significantly more challenging for attackers to compromise the security of your data.

---
## Best Practices 

 * **Validate Tokens Rigorously:** Always check the token’s signature and expiration date before trusting its claims.
 * **Use Strong Algorithms:** Avoid deprecated algorithms. Stick to modern, secure options like RS256 for signing and AES for encryption.
 * **Keep Secrets Safe:** Securely store keys and secrets used in signing or encryption. Compromised keys can lead to security breaches.
 * **Implement Token Revocation:** Consider strategies for revoking tokens if a breach is detected.
 * **Least Privilege Principle:** Only include the necessary information in your token and limit permissions.
 * **Regularly Update Dependencies:** Keep libraries and dependencies up to date to mitigate vulnerabilities.

---
## Resources

- [Stack Overflow Discussion](https://stackoverflow.com/questions/74257560/what-is-the-difference-between-jose-jwa-jwe-jwk-jws-and-jwt)
- [Scott Brady's Post](https://www.scottbrady.io/jose/json-web-encryption)
- [Brilian Firdaus Post](https://codecurated.com/blog/introduction-to-jwt-jws-jwe-jwa-jwk/)
- [Bruno Brito's Post](https://www.brunobrito.net.br/jose-jwt-jws-jwe-jwa-jwk-jwks/)
- [Yashesvinee's Post](https://www.loginradius.com/blog/engineering/guest-post/what-are-jwt-jws-jwe-jwk-jwa/)
- [Prabath Siriwardena's Post](https://medium.facilelogin.com/jwt-jws-and-jwe-for-not-so-dummies-b63310d201a3)
- [Supabase JWT vs JWS](https://www.restack.io/docs/supabase-knowledge-difference-jwt-jws-supabase)
- Photo by [Jose Fontano](https://unsplash.com/pt-br/@josenothose?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) in [Unsplash](https://unsplash.com/pt-br/fotografias/macro-tiro-de-cadeado-de-aco-inoxidavel-pZld9PiPDno?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)