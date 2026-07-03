# Self-Signed X.509 Certificate — PKI Hands-On

## Objective

Generate a self-signed TLS certificate and private key using OpenSSL to reinforce PKI theory (certificates, key pairs, TLS handshake fundamentals) with a hands-on artifact.

## Command

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

## Breakdown

| Flag | Meaning |
|---|---|
| `req` | Start a certificate signing request (CSR) operation |
| `-x509` | Output a self-signed X.509 certificate directly, instead of a CSR that would normally go to a Certificate Authority (CA) for signing |
| `-newkey rsa:4096` | Generate a new RSA key pair, 4096-bit key length, alongside the request |
| `-keyout key.pem` | Write the generated **private key** to `key.pem` |
| `-out cert.pem` | Write the generated **public certificate** to `cert.pem` |
| `-days 365` | Certificate validity period — 1 year |
| `-nodes` | "No DES" — do not encrypt the private key file with a passphrase |

## Key Concepts Reinforced

**X.509** is the standard that defines a certificate's structure — subject, issuer, validity period, public key, and signature. It is the format the certificate is packaged in, not the cryptographic algorithm itself.

**RSA-4096** is the actual asymmetric cryptographic algorithm used here — a public/private key pair where data encrypted with the public key can only be decrypted with the private key, and vice versa for signing.

**Self-signed vs CA-signed:** A real production certificate is signed by a trusted Certificate Authority (CA), which is why browsers trust it without warning. A self-signed certificate is signed by its own private key — functionally identical cryptography, but no third party vouches for it, so browsers flag it as untrusted. Self-signed certs are appropriate for local dev/testing, internal tooling, or lab environments — never for a public-facing production service.

**Why `-nodes` matters operationally:** Without `-nodes`, OpenSSL encrypts the private key file itself with a passphrase, prompting for it every time the key is used (e.g., every service restart). This is more secure for long-lived production keys, but impractical for automated or frequently-restarted lab services. The trade-off: an unencrypted key file must be protected by filesystem permissions and never exposed — the security burden shifts from "passphrase" to "who can read this file."

## Operational Security Note

The private key (`key.pem`) generated in this lab is never committed to this repository. Only the command, its breakdown, and conceptual notes are documented here. A private key exposed in a public repository is equivalent to publishing the master credential for anything that certificate protects — this mirrors the exact failure pattern covered in the [Shared Responsibility Model writeup](./01-shared-responsibility-model.md): a customer-side secrets-management failure, not an AWS/infrastructure issue.

## How This Maps to TLS in Production (AWS Context)

In real AWS environments, this manual process is replaced by **AWS Certificate Manager (ACM)** — which provisions and auto-renews CA-signed certificates for services like CloudFront, ALB, and API Gateway, removing the manual key-handling risk entirely. Understanding the manual OpenSSL process first makes it clear *why* ACM exists: it automates away exactly the kind of private-key exposure risk this lab required deliberate care to avoid.
