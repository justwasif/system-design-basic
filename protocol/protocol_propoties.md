# Detailed Explanation of Protocol Properties

This is an excellent lecture transcript about communication protocol properties. Let me break down the key concepts in detail.

## What is a Protocol?

A **communication protocol** is a system of rules that allows two or more parties to communicate. Think of it as a shared language or a set of agreements:
- "If you follow rules 1,2,3,4,5 and I follow the same rules, we can communicate"
- Every protocol was designed to solve a specific problem

**Example:** TCP was designed in the 1960s to solve networking problems of that era. Today, data centers are pushing TCP to its limits because it was designed for low-bandwidth networks, not modern high-speed data center traffic. This is why new protocols like **Homa** (2022) are being invented.

---

## Core Protocol Properties

### 1. Data Format

This describes how data looks "on the wire" (assuming no encryption):

| Format | Characteristics | Examples |
|--------|----------------|----------|
| **Text-based** | Human-readable, plaintext | JSON, XML, HTTP, SMTP |
| **Binary** | Machine-readable only, more efficient | Protocol Buffers (protobuf), RESP (Redis), gRPC, HTTP/2, HTTP/3 |

**Key insight:** HTTP/2 and HTTP/3 changed the underlying wire format to binary, but kept the API compatibility - your `GET` requests still work the same way.

### 2. Transfer Mode

#### Message-based (Datagram)
- Each message has a clear **start** and **end**
- Discrete, self-contained messages
- **Examples:** UDP, DHCP
- UDP messages fit into IP packets; if too large for MTU (Maximum Transmission Unit), they're fragmented and reassembled using fragment IDs

#### Stream-based
- Continuous flow of bytes - like a river
- No inherent message boundaries
- **Examples:** TCP, WebRTC (video/audio streams)

**The parsing problem:** When you build HTTP on top of TCP, the client must parse the byte stream to figure out where requests start and end. This parsing overhead is actually a limitation of TCP/IP being stream-based.

**Modern solution:** New protocols like Homa try to create "message-based TCP" - preserving message boundaries rather than just a byte stream.

### 3. Addressing System

Protocols need to identify sources and destinations. This happens at multiple layers:

| Layer | Addressing Type | Example |
|-------|----------------|---------|
| Layer 7 (Application) | DNS names | `www.google.com` |
| Layer 3 (Network) | IP addresses | `9.7.3.2` |
| Layer 2 (Data Link) | MAC addresses | Used by ARP to find hardware address for an IP |

**How addressing chains together:**
1. You use DNS (human-readable name)
2. DNS resolves to an IP address
3. ARP resolves the IP to a MAC address
4. The frame uses MAC addresses, the packet inside uses IP addresses

### 4. Directionality

| Type | Description |
|------|-------------|
| **Unidirectional** | One-way communication only |
| **Half-duplex** | Both directions, but only one at a time (e.g., Wi-Fi) |
| **Full-duplex** | Both directions simultaneously |

### 5. Protocol State

| Type | Characteristics | Examples |
|------|----------------|----------|
| **Stateful** | Maintains connection state, remembers past interactions | TCP, gRPC, Apache Thrift |
| **Stateless** | Each request is independent, no memory of previous ones | UDP |

### 6. Routing & Proxy Handling

This relates to addressing and how protocols work with intermediate nodes:

- Your **final destination** might be `google.com`
- But your **immediate destination** from a TCP perspective might be a proxy
- The protocol must handle this indirection gracefully

**Example flow:**
```
Client → Proxy → Google.com
```
Your TCP connection goes to the proxy, not directly to Google.

### 7. Flow & Congestion Control

| Protocol | Features |
|----------|----------|
| **TCP** | Flow control, congestion control, guaranteed retransmission, reliable delivery |
| **UDP** | None of these - "whatever you send, you hope it arrives" |

### 8. Error Management

- **Error codes** (e.g., HTTP status codes, DHCP error messages)
- **Timeout handling** - Should we retry or not?
- **Recovery strategies**

---

## Important Caveats from the Lecturer

The speaker emphasizes these points strongly:

1. **Don't memorize these properties** - This is theoretical
2. **Don't live by these rules blindly** - Performance and solving real use cases matter more
3. **These come from personal experience** - Not an exhaustive list; you may discover properties not listed here
4. **The purpose of the protocol determines everything** - What problem are you solving?

---

## Summary Table

| Property | Key Question | Examples |
|----------|--------------|----------|
| Data Format | Human-readable or machine-efficient? | Text (HTTP) vs Binary (gRPC) |
| Transfer Mode | Messages with boundaries or continuous stream? | UDP (message) vs TCP (stream) |
| Addressing | How do you identify source/destination? | DNS, IP, MAC |
| Directionality | One-way, alternating, or simultaneous? | Half vs Full duplex |
| State | Does connection remember history? | TCP (stateful) vs UDP (stateless) |
| Routing | How does it work with proxies/gateways? | HTTP proxies |
| Flow Control | Can it prevent overwhelming the receiver? | TCP yes, UDP no |
| Error Management | How are failures handled? | Error codes, timeouts, retries |

Understanding these properties helps you reason about existing protocols and, if you ever need to build your own, make informed design decisions.