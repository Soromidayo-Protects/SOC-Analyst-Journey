# Day 1 — Home Lab Setup & Wireshark Basics

## What I did
- Attempted to set up a home SOC lab (Kali Linux + Security Onion)
- Hit connectivity issues downloading the 15GB Security Onion ISO — 
  verified the download was corrupted using SHA256 hash comparison in PowerShell
- Fixed a GPG signing key issue and a package conflict while updating Kali Linux
- Since Security Onion wasn't available yet, pivoted to learning Wireshark 
  fundamentals on my own live traffic

## Wireshark basics practiced
- Installed Wireshark and started a live capture on my Wi-Fi interface
- Learned to read the core columns: Source, Destination, Protocol, Info
- Practiced filtering traffic:
  - `dns` — isolate DNS lookups
  - `tcp` — isolate TCP connections
  - `tls` — isolate encrypted HTTPS handshakes
  - `ip.addr == x.x.x.x` — isolate traffic to/from a specific IP

## Key takeaway
Even without decrypting traffic, Source/Destination/Port/Protocol reveal 
a lot. For example, traffic to port 5222 (XMPP) alongside a DNS lookup 
for whatsapp.com strongly suggested WhatsApp app traffic — this is the 
kind of pattern-based reasoning SOC analysts use daily.

## Next steps
- Re-download and verify Security Onion ISO
- Set up Security Onion as a VM
- Connect Kali + Security Onion on an internal network
