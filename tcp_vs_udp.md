# TCP vs UDP

## Quick Decision

```
Data must arrive correctly and in order?     → TCP
Speed matters more than perfect delivery?   → UDP
```

---

## TCP (Transmission Control Protocol)

### How It Works
1. **3-Way Handshake** establishes connection before data transfer:
```
Client → SYN        → Server
Client ← SYN-ACK    ← Server
Client → ACK        → Server
(connection established — data flows)
```
2. Data sent in segments, each acknowledged
3. Lost segments are retransmitted
4. Flow control prevents overwhelming the receiver
5. 4-way handshake to close connection

### Characteristics
- **Connection-oriented** — must establish before sending
- **Reliable** — guarantees all data arrives
- **Ordered** — segments reassembled in correct sequence
- **Error-checked** — checksums on every segment
- **Slower** — overhead of all the above

### Use Cases
- Web browsing (HTTP/HTTPS)
- File transfers (FTP, SFTP, SCP)
- Email (SMTP, IMAP)
- Database connections
- SSH

---

## UDP (User Datagram Protocol)

### How It Works
- No connection setup — just send packets directly
- No acknowledgment, no retransmission
- No ordering guarantees
- Fire-and-forget

### Characteristics
- **Connectionless** — no handshake
- **Unreliable** — packets may be lost, no retransmission
- **Unordered** — packets may arrive out of order
- **Faster** — minimal overhead
- **Lower latency** — no waiting for ACKs

### Use Cases
- Video/audio streaming (Netflix, YouTube, VoIP, Zoom)
- Online gaming (position updates — better to skip than delay)
- DNS queries (fast lookup, just one request/response)
- Live broadcast / IPTV
- IoT sensor data

---

## Comparison

| | TCP | UDP |
|-|-----|-----|
| **Connection** | Required (3-way handshake) | None |
| **Reliability** | Guaranteed delivery | No guarantee |
| **Ordering** | Guaranteed in-order | No guarantee |
| **Error correction** | Yes (retransmit) | Checksum only |
| **Speed** | Slower | Faster |
| **Overhead** | High | Low |
| **Use case** | Accuracy-critical | Latency-critical |

---

## Why UDP for Video Streaming?
- A late packet is **worse than a lost packet** — retransmitting a video frame that's already past makes no sense
- Minor packet loss = momentary visual artifact, barely noticeable
- The application handles buffering and quality adaptation (ABR)
- **QUIC** (HTTP/3) implements reliability **selectively on top of UDP** — the best of both worlds

---

## QUIC / HTTP/3
Google's QUIC protocol runs over UDP but adds:
- Reliable stream multiplexing
- Faster handshake (0-RTT reconnect)
- Better performance on lossy connections
- Now standardized as HTTP/3

---

## Interview Tip
> "For the live video streaming feature, I'd use UDP-based delivery (DASH or HLS over UDP/QUIC). A missed video segment results in a brief visual glitch — far better than a multi-second freeze waiting for TCP to retransmit a packet that's already stale. The client-side player handles buffering and adaptive bitrate switching to compensate for packet loss."
