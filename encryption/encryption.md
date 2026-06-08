# Encryption

*Sources: CS50 Intro to Cybersecurity*

## Hashing

A hash function takes an input and produces a fixed-length output. It is a one-way operation — you cannot reverse a hash to get the original input.

Used for storing passwords. Instead of storing the password itself, you store the hash. On login, the input is hashed and compared against the stored hash.

Common algorithms: MD5 (broken, do not use), SHA-1 (deprecated), SHA-256, bcrypt (preferred for passwords).

### Salts

A salt is a random value added to a password before hashing. It is stored alongside the hash.

**Why it matters:** Without salts, two users with the same password produce the same hash. An attacker with a precomputed table of hashes can look up the original password instantly.

**Rainbow tables** are precomputed tables mapping hashes back to their original inputs. Salting defeats rainbow tables — because the salt randomizes the input, the resulting hash will never match a precomputed entry. Two users with the same password will have completely different hashes.

---

## Symmetric Encryption

Both parties use the **same key** to encrypt and decrypt. Fast and efficient, but requires the key to be shared securely beforehand.

**The problem:** How do you share the key without someone intercepting it?

Common algorithms: AES, ChaCha20.

---

## Asymmetric Encryption

Uses a **key pair** — a public key and a private key. Mathematically linked so that what one key does, the other can undo.

- **Public key** — shared openly with anyone
- **Private key** — never leaves your machine

### Encryption
Anyone can encrypt a message using your **public key**. Only your **private key** can decrypt it. Anyone can lock a message for you, but only you can open it.

### Signatures
You sign a message using your **private key**. Anyone with your **public key** can verify the signature. This proves the message came from you — as long as your private key hasn't been compromised.

**Encryption vs Signing:**
- Encryption = confidentiality. Only the intended recipient can read it.
- Signing = authenticity. Proof the message came from a specific sender.

Same underlying math, opposite use case.

Common algorithms: RSA, ECC.

---

## Passkeys

A modern replacement for passwords using asymmetric cryptography. Instead of a password, your device generates a key pair. The public key is registered with the service, and the private key never leaves your device.

Authentication is performed by signing a challenge with your private key. The service verifies it with your public key. Nothing secret is ever transmitted, so there is nothing to steal or phish.

---

## Encryption at Rest

Data is encrypted while stored on disk. If an attacker gains physical access to the storage medium, the data is unreadable without the decryption key.

Common tools: LUKS (Linux), BitLocker (Windows), FileVault (macOS).

Complements encryption in transit (TLS) — one protects stored data, the other protects data moving across a network.
