# Day 2 -Deeper Wireshark Investigation Skills

## What I did
- Confirmed the Security Onion ISO still needs a clean re-download (data/network issues)
- Continued building Wireshark skills while waiting, going deeper than Day 1

## Skills practiced

### Follow TCP Stream
- Learned to reconstruct a full conversation from scattered packets
- Followed an HTTPS stream and saw it was unreadable, confirmed this is expected 
  since TLS encrypts payload content, not a tool failure

### Coloring Rules
- Reviewed View > Coloring Rules to understand what each row color means 
  (e.g. red = TCP RST/reset, purple = TCP, light blue = UDP/DNS)

### Protocol Hierarchy (Statistics > Protocol Hierarchy)
- Learned to read nested percentages correctly (each layer ≤ the layer above it)
- On full capture: IPv4 91.7%, TCP 62.6%, UDP 26.4%, TLS made up ~40% of total 
  bytes despite being a smaller % of packet count, showing TLS carries large 
  data volumes in fewer packets
- Learned End Packets/Bytes = packets where that protocol was the final layer 
  (nothing deeper found), vs total Packets = everything counted at that layer

### Investigating QUIC traffic
- Noticed QUIC IETF made up 21.5% of packets, an unfamiliar protocol
- Traced source IPs (142.251.x.x, 192.178.x.x) to Google's known IP ranges
- Attempted to find Server Name Indication (SNI) inside QUIC Initial/Handshake 
  packets: confirmed QUIC encrypts this by design, unlike older TLS, so it 
  wasn't visible. Good real-world lesson: knowing when content is deliberately 
  unreadable is as important as knowing how to read it

### DNS response structure
- Read a full DNS response packet: Transaction ID, "No error" flag, Answer count
- Cross-referenced domains resolved during capture: web.whatsapp.com, 
  accounts.google.com, dns.google

### Conversations view (Statistics > Conversations > TCP tab)
- Sorted by Bytes to find the single largest conversation in the capture
- Top result: 57.144.39.32:5222 <-> my device, 73 kB / 372 packets — matched 
  to WhatsApp (same IP/port identified on Day 1), confirming a long-lived 
  background connection
- Rest of traffic was small HTTPS (443) connections to Google/Microsoft CDN 
  ranges — normal background app/browser activity

## Key takeaway
Conversations view is often the fastest starting point in a real investigation — 
it surfaces the biggest data mover instantly, before reading a single packet in 
detail. Combined with Protocol Hierarchy (what's happening) and DNS/IP lookups 
(who's involved), this is a genuine first-pass triage workflow.

## Next steps
- Get Security Onion ISO downloaded and verified
- Set up Security Onion as a VM
- Connect Kali + Security Onion on an internal network
