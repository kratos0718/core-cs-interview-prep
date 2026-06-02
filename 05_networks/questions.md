# Computer Networks — 70 Questions (basic → advanced)

⭐ = very frequently asked.

---

## A. Fundamentals & Models

**1. ⭐ What is a computer network?**
A collection of interconnected devices (computers, servers, phones) that can share data and resources. Connections can be wired or wireless. The internet is the largest network of networks.

**2. Types of networks by scale?**
**LAN** (Local Area Network — a building/campus), **MAN** (Metropolitan — a city), **WAN** (Wide Area — countries/global, e.g. the internet), **PAN** (Personal — Bluetooth range). Smaller = faster, cheaper, more private.

**3. ⭐ What is the OSI model? Name the 7 layers.**
A conceptual 7-layer framework standardizing how data travels across a network. Top to bottom: **Application** (7), **Presentation** (6), **Session** (5), **Transport** (4), **Network** (3), **Data Link** (2), **Physical** (1). Mnemonic: "All People Seem To Need Data Processing." Each layer has a specific role and talks only to adjacent layers.

**4. ⭐ Explain each OSI layer's job.**
- **Physical**: raw bits over the medium (cables, voltages, radio).
- **Data Link**: node-to-node delivery on the same network; framing, MAC addresses, error detection (e.g. Ethernet, switches).
- **Network**: routing across networks; logical addressing (IP), packets (routers).
- **Transport**: end-to-end delivery; reliability, segmentation (TCP/UDP, ports).
- **Session**: establishes/manages/terminates sessions (dialogue control).
- **Presentation**: data format translation, encryption, compression.
- **Application**: user-facing protocols (HTTP, FTP, SMTP, DNS).

**5. ⭐ OSI vs TCP/IP model?**
The **TCP/IP** model is the practical one the internet runs on, with 4 (or 5) layers: **Application** (combines OSI 5-7), **Transport**, **Internet** (=OSI Network), **Link/Network Access** (=OSI 1-2). OSI is a 7-layer reference/teaching model; TCP/IP is what's actually implemented.

**6. What is encapsulation in networking?**
As data goes *down* the layers, each layer adds its own header (and sometimes trailer) — application data → segment (Transport adds port) → packet (Network adds IP) → frame (Data Link adds MAC). The receiver reverses this (decapsulation). Like putting a letter in nested envelopes.

**7. Packet vs Frame vs Segment vs Datagram?**
Different names for the data unit at each layer (PDU — Protocol Data Unit): **Segment** (Transport/TCP), **Datagram** (Transport/UDP, or Network/IP), **Packet** (Network/IP), **Frame** (Data Link), **Bits** (Physical). Knowing which layer each belongs to is the point.

---

## B. Transport Layer: TCP/UDP (very high-frequency)

**8. ⭐ TCP vs UDP — the headline difference?**
**TCP** (Transmission Control Protocol): connection-oriented, **reliable** (guarantees delivery, order, no duplicates), with error-checking and flow/congestion control — but slower (more overhead). **UDP** (User Datagram Protocol): connectionless, **unreliable** (no guarantee of delivery or order), no handshake — but fast and lightweight. TCP for web/email/files; UDP for video calls/gaming/DNS/streaming where speed > perfect delivery.

**9. ⭐ When would you choose UDP over TCP?**
When speed and low latency matter more than guaranteed delivery, and the app can tolerate some loss: live video/audio, online gaming, VoIP, DNS lookups, real-time telemetry. Losing one video frame is fine; waiting for a retransmission would cause lag.

**10. ⭐ What is the TCP 3-way handshake?**
How TCP establishes a connection: **SYN** (client → server: "let's connect, my sequence number is X"), **SYN-ACK** (server → client: "OK, ack X+1, my number is Y"), **ACK** (client → server: "ack Y+1"). After these three messages, the connection is established and data can flow. It synchronizes sequence numbers on both sides.

**11. What is TCP connection termination (4-way handshake)?**
Closing uses **FIN/ACK** in both directions: one side sends FIN, the other ACKs, then it sends its own FIN, which is ACKed. Four messages because each side closes its direction independently (TCP is full-duplex).

**12. ⭐ How does TCP ensure reliability?**
Multiple mechanisms: **sequence numbers** (order + detect missing data), **acknowledgments (ACKs)** (confirm receipt), **retransmission** (resend un-acked data after a timeout), **checksums** (detect corruption), **flow control** (don't overwhelm the receiver), and **congestion control** (don't overwhelm the network).

**13. What is flow control? (TCP)**
Preventing a fast sender from overwhelming a slow receiver. TCP uses a **sliding window**: the receiver advertises how much buffer space it has (window size), and the sender won't send more than that unacknowledged. It's receiver-driven.

**14. What is congestion control? (TCP)**
Preventing the sender from overwhelming the *network* (not just the receiver). Algorithms like **slow start** (ramp up gradually), **congestion avoidance**, and **fast retransmit/recovery** adjust the sending rate based on detected packet loss. Network-driven.

**15. Flow control vs Congestion control?**
**Flow control** protects the *receiver* (don't send faster than it can consume). **Congestion control** protects the *network* (don't send faster than the network can carry). Both throttle the sender, for different reasons.

**16. What is a sliding window?**
A flow-control mechanism where the sender can have multiple unacknowledged packets "in flight" up to the window size, rather than waiting for an ACK after each one. As ACKs arrive, the window "slides" forward. Improves throughput.

**17. What is a port number?**
A 16-bit number (0-65535) identifying a specific application/service on a device, so multiple network services can run on one IP. Well-known ports: HTTP 80, HTTPS 443, FTP 21, SSH 22, DNS 53, SMTP 25. The IP gets you to the machine; the port gets you to the right program.

**18. What is a socket?**
The combination of an **IP address + port number** that uniquely identifies one endpoint of a network connection. A connection is defined by a pair of sockets (source IP:port ↔ destination IP:port). Programs use the socket API to send/receive.

---

## C. Network Layer & IP

**19. ⭐ What is an IP address?**
A unique numerical label identifying a device on a network, used for routing. **IPv4**: 32-bit, like `192.168.1.1` (~4.3 billion addresses, running out). **IPv6**: 128-bit, like `2001:0db8::1` (vastly more). It's the device's "postal address."

**20. ⭐ IPv4 vs IPv6?**
**IPv4**: 32-bit, ~4.3 billion addresses (exhausted), dotted decimal. **IPv6**: 128-bit, astronomically more addresses, hexadecimal with colons, built-in features (no NAT needed, better routing/security). IPv6 exists primarily because IPv4 ran out of addresses.

**21. Public vs Private IP?**
**Public IP**: globally unique, routable on the internet, assigned by ISP. **Private IP**: used within a local network (ranges like `192.168.x.x`, `10.x.x.x`), not routable on the internet, reused everywhere. NAT translates between them.

**22. ⭐ What is NAT?**
Network Address Translation — lets many devices on a private network share **one public IP**. The router rewrites private source IPs/ports to its public IP (and back for replies), tracking the mappings. It's why your home devices all browse via one public address — and a stopgap for IPv4 exhaustion.

**23. What is a subnet / subnet mask?**
**Subnetting** divides a large network into smaller sub-networks for organization, security, and efficiency. The **subnet mask** (e.g. `255.255.255.0`) separates the IP into the *network* portion and the *host* portion. `/24` (CIDR) means the first 24 bits are the network.

**24. What is CIDR?**
Classless Inter-Domain Routing — a notation (`192.168.1.0/24`) specifying how many bits are the network prefix, replacing the old rigid Class A/B/C system for flexible address allocation.

**25. ⭐ MAC address vs IP address?**
**MAC** (Media Access Control): a permanent, physical hardware address burned into the network card (48-bit, like `00:1A:2B:...`), used for *local* delivery within a network (Layer 2). **IP**: a logical, changeable address used for *routing across* networks (Layer 3). Analogy: MAC = your name (fixed); IP = your current mailing address (changes when you move).

**26. ⭐ What is ARP?**
Address Resolution Protocol — maps a known **IP address** to its **MAC address** within a local network. When a device wants to send to an IP on the LAN, it broadcasts "who has this IP?" and the owner replies with its MAC. Bridges Layer 3 (IP) to Layer 2 (MAC).

**27. What is a router vs switch vs hub?**
**Hub** (Layer 1): dumb — broadcasts incoming data to *all* ports (obsolete, causes collisions). **Switch** (Layer 2): smart — forwards frames only to the correct port using MAC addresses (within a LAN). **Router** (Layer 3): connects *different* networks and routes packets between them using IP addresses (e.g. your LAN to the internet).

**28. What is routing?**
The process of selecting a path for data to travel across networks from source to destination. Routers use **routing tables** and protocols (OSPF, BGP, RIP) to decide the next hop. BGP routes between large networks (ISPs) on the internet.

**29. What is the TTL field?**
Time To Live — a counter in an IP packet decremented by each router it passes. When it hits 0, the packet is discarded (and an error sent back). Prevents packets from looping forever in the network. `traceroute` exploits TTL to map the path.

---

## D. Application Layer & Web (high-frequency)

**30. ⭐ What is DNS?**
Domain Name System — translates human-readable domain names (`google.com`) into IP addresses (`142.250.x.x`) that computers use to route. It's the internet's "phonebook." Uses UDP port 53 (mostly).

**31. ⭐ Walk through a DNS lookup.**
1. Browser checks its cache, then the OS cache. 2. If not found, asks the **resolver** (usually your ISP's DNS server). 3. Resolver asks a **root** server → directs to the **TLD** server (`.com`). 4. TLD server → directs to the domain's **authoritative** name server. 5. Authoritative server returns the IP. 6. Resolver caches it and returns it to you. (Recursive + iterative queries.)

**32. ⭐ What happens when you type a URL and press Enter? (THE classic question)**
1. Browser parses the URL. 2. **DNS lookup** resolves the domain to an IP. 3. Browser opens a **TCP connection** (3-way handshake) to that IP on port 80/443. 4. If HTTPS, a **TLS handshake** sets up encryption. 5. Browser sends an **HTTP request** (GET /). 6. Server processes it and returns an **HTTP response** (HTML). 7. Browser **renders** the page — parsing HTML, fetching CSS/JS/images (more requests), building the DOM. 8. Connection closed or reused. *(Mentioning DNS, TCP, TLS, HTTP, and rendering hits every layer — that's what impresses.)*

**33. ⭐ HTTP vs HTTPS?**
**HTTP**: HyperText Transfer Protocol — the protocol for web data, but **unencrypted** (anyone in between can read it). **HTTPS**: HTTP over **TLS/SSL** — encrypted, authenticated, and integrity-checked. HTTPS uses port 443; HTTP uses 80. Always use HTTPS for anything sensitive.

**34. What is TLS/SSL? What is the TLS handshake?**
**TLS** (Transport Layer Security, successor to SSL) encrypts data between client and server. The **handshake**: client and server agree on a cipher, the server proves its identity via a **certificate** (signed by a Certificate Authority), they exchange keys (asymmetric crypto) to establish a shared **session key**, then switch to fast symmetric encryption for the actual data.

**35. ⭐ What are HTTP methods?**
**GET** (retrieve data, no body, idempotent), **POST** (submit/create data, has a body), **PUT** (replace/update a resource, idempotent), **PATCH** (partial update), **DELETE** (remove), **HEAD** (like GET but headers only), **OPTIONS** (what methods are allowed). REST APIs map these to CRUD.

**36. GET vs POST?**
**GET**: parameters in the URL, no body, cached, bookmarkable, idempotent, for *retrieving* — shouldn't change server state. **POST**: data in the body, not cached/bookmarked, not idempotent, for *creating/submitting* — changes state. Don't send passwords via GET (they'd appear in URLs/logs).

**37. ⭐ What are HTTP status codes?**
3-digit codes indicating a request's result. **1xx** informational, **2xx** success (200 OK, 201 Created), **3xx** redirection (301 permanent, 302 temporary, 304 Not Modified), **4xx** client error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests), **5xx** server error (500 Internal, 502 Bad Gateway, 503 Service Unavailable).

**38. What is idempotency?**
An operation is **idempotent** if doing it multiple times has the same effect as doing it once. GET, PUT, DELETE are idempotent; POST is not (two POSTs may create two records). Important for safe retries.

**39. ⭐ What is the difference between stateful and stateless? Is HTTP stateless?**
**Stateless**: the server doesn't remember previous requests; each request is independent. **HTTP is stateless** — which is why we need cookies/sessions/tokens to maintain user state (like login) across requests.

**40. ⭐ Cookies vs Sessions vs Tokens?**
**Cookie**: small data stored in the browser, sent with each request (e.g. a session ID). **Session**: server-side storage of user state, referenced by a session ID (usually in a cookie) — server remembers. **Token (e.g. JWT)**: a self-contained, often stateless credential the client stores and sends (in a header); the server verifies it without server-side storage — scales better.

**41. What is a JWT?**
JSON Web Token — a compact, self-contained, digitally-signed token carrying claims (like user ID, roles). Three parts: header, payload, signature. The server can verify it without a database lookup (stateless auth). Used widely for APIs.

**42. What is REST?**
Representational State Transfer — an architectural style for APIs using HTTP methods on **resources** (URLs), stateless requests, and standard status codes, typically returning JSON. RESTful = predictable, resource-oriented endpoints (`GET /users/1`).

**43. REST vs GraphQL (brief)?**
**REST**: multiple fixed endpoints, may over- or under-fetch data. **GraphQL**: a single endpoint where the client specifies *exactly* what data it wants in one query — avoids over/under-fetching, at the cost of more server complexity.

**44. What is WebSocket? How does it differ from HTTP?**
**WebSocket**: a protocol providing a persistent, **full-duplex** (two-way) connection between client and server, so the server can push data anytime (chat, live updates). HTTP is request-response (client must ask); WebSocket stays open for real-time bidirectional communication.

**45. What is an API?**
Application Programming Interface — a defined way for software components to communicate. A web API exposes endpoints (URLs) a client calls to get/send data. It's a contract: "send this request, get this response."

---

## E. Switching, Devices, and DataLink

**46. Circuit switching vs Packet switching?**
**Circuit switching**: a dedicated path is reserved for the whole communication (old telephone networks) — guaranteed bandwidth but wasteful when idle. **Packet switching**: data is split into packets routed independently, sharing links (the internet) — efficient, resilient, but variable delay. The internet uses packet switching.

**47. What is bandwidth vs latency vs throughput?**
**Bandwidth**: maximum data rate a link *can* carry (capacity, e.g. 100 Mbps). **Latency**: delay for data to travel from source to destination (e.g. 20 ms ping). **Throughput**: the *actual* achieved data rate (≤ bandwidth, reduced by congestion/overhead). Bandwidth = pipe width; latency = pipe length/delay; throughput = water actually flowing.

**48. What is jitter?**
Variation in packet delay (latency) over time. High jitter hurts real-time apps (choppy video/audio) even if average latency is fine. Buffers smooth it out.

**49. What is a collision domain vs broadcast domain?**
**Collision domain**: a network segment where packets can collide (hubs put everyone in one; switches separate them per port). **Broadcast domain**: the set of devices that receive a broadcast (a switch is one broadcast domain; a router separates them). Switches break collision domains; routers break broadcast domains.

**50. What is Ethernet?**
The dominant wired LAN technology (Layer 1-2), defining how devices frame and transmit data over cables using MAC addresses and CSMA/CD (historically) for access control.

**51. What is CSMA/CD and CSMA/CA?**
Media access methods. **CSMA/CD** (Collision Detection — wired Ethernet): listen before sending; if a collision happens, stop and retry after a random wait. **CSMA/CA** (Collision Avoidance — Wi-Fi): try to avoid collisions proactively (since wireless can't easily detect them) using acknowledgments and waiting.

**52. What is error detection? (CRC, checksum, parity)**
Methods to detect corrupted data. **Parity bit**: a single bit making the count of 1s even/odd (detects single-bit errors). **Checksum**: sum of data used to verify integrity. **CRC** (Cyclic Redundancy Check): a stronger polynomial-based check used in Ethernet frames. Detect (and sometimes correct) transmission errors.

---

## F. Security & Advanced

**53. ⭐ Symmetric vs Asymmetric encryption?**
**Symmetric**: same key encrypts and decrypts — fast, but the key must be shared securely (AES). **Asymmetric**: a public/private key pair — encrypt with public, decrypt with private (or sign with private, verify with public); slower but solves key distribution (RSA). TLS uses asymmetric to exchange a symmetric session key, then symmetric for speed.

**54. What is a digital signature?**
Data signed with the sender's **private key**; anyone can verify it with the sender's **public key**. It proves authenticity (it's really from them) and integrity (not tampered). Basis of HTTPS certificates.

**55. What is a certificate / Certificate Authority (CA)?**
A **digital certificate** binds a public key to an identity (a domain). A **CA** is a trusted third party that signs certificates, so browsers can trust that `google.com`'s public key really belongs to Google. The chain of trust underpins HTTPS.

**56. What is a firewall?**
A security system (hardware/software) that monitors and filters network traffic based on rules, blocking unauthorized access while allowing legitimate traffic. Operates by IP, port, protocol, etc. Your network's gatekeeper.

**57. ⭐ What is a DDoS attack?**
Distributed Denial of Service — overwhelming a server/network with a flood of traffic from many sources (often a botnet) so legitimate users can't access it. Mitigated by rate limiting, traffic filtering, and CDNs/scrubbing services.

**58. What is a man-in-the-middle attack?**
An attacker secretly intercepts (and possibly alters) communication between two parties who think they're talking directly. HTTPS/TLS prevents it by encrypting and authenticating the connection (the certificate proves the server's identity).

**59. What is a VPN?**
Virtual Private Network — creates an encrypted "tunnel" over the public internet, so your traffic is private and appears to originate from the VPN server. Used for security, privacy, and accessing remote/private networks.

**60. What is a proxy server?**
An intermediary between client and server. A **forward proxy** acts on behalf of clients (privacy, caching, filtering). A **reverse proxy** sits in front of servers (load balancing, caching, SSL termination, hiding backend). 

**61. What is a load balancer?**
Distributes incoming traffic across multiple servers so no single one is overwhelmed, improving availability and scalability. Strategies: round-robin, least-connections, IP-hash. A type of reverse proxy.

**62. What is a CDN?**
Content Delivery Network — a geographically distributed set of servers that cache content close to users, reducing latency and offloading the origin server. e.g. images/videos served from a nearby edge server.

---

## G. Misc / quick concepts

**63. What is DHCP?**
Dynamic Host Configuration Protocol — automatically assigns IP addresses (and gateway/DNS settings) to devices joining a network, so you don't configure each manually. Uses a DORA process (Discover, Offer, Request, Acknowledge).

**64. What is ICMP?**
Internet Control Message Protocol — used for diagnostics and error reporting (not data transfer). `ping` (echo request/reply) and `traceroute` rely on it. e.g. "destination unreachable" messages.

**65. What is ping / traceroute?**
**ping**: tests reachability and round-trip time to a host using ICMP echo. **traceroute**: maps the path (each router hop) to a destination by sending packets with increasing TTL and seeing where each expires.

**66. HTTP/1.1 vs HTTP/2 vs HTTP/3 (brief)?**
**HTTP/1.1**: one request at a time per connection (head-of-line blocking). **HTTP/2**: multiplexing (many requests over one connection), header compression — faster. **HTTP/3**: runs over **QUIC** (UDP-based), eliminating TCP head-of-line blocking, faster connection setup.

**67. What is head-of-line blocking?**
When one slow/lost packet or request holds up everything behind it. TCP and HTTP/1.1 suffer it; HTTP/2 reduces it at the HTTP layer; HTTP/3 (over UDP/QUIC) eliminates it at the transport layer.

**68. Connection-oriented vs Connectionless?**
**Connection-oriented** (TCP): establish a connection (handshake) before data, ensure ordered reliable delivery. **Connectionless** (UDP): just send packets, no setup, no guarantees. Trade reliability for speed/simplicity.

**69. What is the difference between a domain and a hostname?**
A **domain** is a registered name (`example.com`). A **hostname** identifies a specific machine, often a subdomain (`mail.example.com`, `www.example.com`). FQDN = Fully Qualified Domain Name (the complete address).

**70. ⭐ What is the difference between latency and bandwidth (with analogy)?**
**Bandwidth** is how *much* data can flow at once (the *width* of the highway — lanes). **Latency** is how *long* it takes to get there (the *length*/speed limit of the trip). A wide highway (high bandwidth) doesn't help if the destination is far (high latency). You need both for good performance.

---

## ⭐ The 12 Networks must-knows:
OSI/TCP-IP layers (3,5) · TCP vs UDP (8) · 3-way handshake (10) · TCP reliability (12) · flow vs congestion control (15) · IP vs MAC (25) · DNS + lookup (30,31) · **what happens when you type a URL** (32) · HTTP vs HTTPS + TLS (33,34) · HTTP methods + status codes (35,37) · stateless/cookies/sessions/JWT (39,40) · symmetric vs asymmetric encryption (53).
