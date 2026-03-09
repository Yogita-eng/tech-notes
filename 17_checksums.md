# Checksums

## What Is It?
A checksum is a **computed value derived from data** that acts as a unique fingerprint. It's used to verify data integrity — detecting corruption or tampering during transmission or storage.

```
Sender:   data → hash(data) → checksum attached
Receiver: data → hash(data) → compare checksums → match = intact, mismatch = corrupted
```

---

## How It Works

1. Sender computes `checksum = hash(data)` and attaches it alongside the data
2. Data + checksum sent over network or written to disk
3. Receiver independently computes `hash(received_data)`
4. **Match** → data is intact ✅
5. **Mismatch** → data was corrupted or tampered with ❌ → request retransmission

---

## Common Checksum Algorithms

| Algorithm | Output Size | Speed | Use Case |
|-----------|------------|-------|---------|
| **CRC32** | 32 bits | Very fast | Network protocols, ZIP, PNG |
| **Adler-32** | 32 bits | Very fast | Zlib/gzip compression |
| **MD5** | 128 bits | Fast | File integrity checks (not for security) |
| **SHA-1** | 160 bits | Medium | Git commit hashes (deprecated for security) |
| **SHA-256** | 256 bits | Slower | Security-critical integrity, TLS |
| **xxHash** | 32/64/128 bits | Fastest | High-throughput non-cryptographic checks |

> **Security note:** MD5 and SHA-1 are broken for cryptographic security (collision attacks exist). Use SHA-256+ for anything security-sensitive. For pure integrity checks (not security), CRC32/xxHash are fine.

---

## Real-World Usage

| System | How Checksums Are Used |
|--------|----------------------|
| **TCP/IP** | Checksum in every packet header detects transmission errors |
| **AWS S3** | MD5/CRC32 checksums on stored objects; verified on upload and retrieval |
| **HDFS (Hadoop)** | Block checksums detect bit rot on disk |
| **Git** | Every commit, tree, and blob is identified by SHA-1 hash |
| **BitTorrent** | Each piece has an SHA-1 hash — verified before assembling file |
| **TLS/HTTPS** | HMAC for message authentication integrity |
| **Cassandra** | CRC checksums on commit log entries |

---

## Checksum vs Hash vs Encryption

| | Checksum | Cryptographic Hash | Encryption |
|-|----------|--------------------|------------|
| Purpose | Detect accidental errors | Detect tampering + fingerprinting | Hide data |
| Reversible? | No | No | Yes (with key) |
| Speed | Very fast | Moderate | Slower |
| Examples | CRC32 | SHA-256 | AES |

---

## Interview Tip
> "When clients upload files to our storage service, we compute a SHA-256 checksum of the file content before storing it. We store the checksum in metadata. On retrieval, we recompute the checksum and compare — if they don't match, we know the stored data has been corrupted and we fetch from a replica."
