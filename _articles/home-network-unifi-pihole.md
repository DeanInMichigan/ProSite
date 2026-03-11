---
title: "Enterprise-Grade Home Network Security for Under $500"
description: "How I built a secure, segmented home network using a Unifi UCG Ultra, USW Lite 8 PoE switch, U7 Lite access point, and a Raspberry Pi running Pi-hole for DNS-level blocking."
tag: "Networking & Security"
order: 3
---

I wanted a home network that gave me real security visibility and control — not just a consumer router with a web UI that hasn't been updated in three years. But I also didn't want a rack of gear I barely understood. The goal was simple: get as close to an enterprise security posture as possible, keep it under $500, and make sure I could actually understand and manage what I built.

After a lot of research I landed on a Unifi stack paired with a Raspberry Pi running Pi-hole as my DNS server. Here's what I built, why I chose each piece, and what it gives me that a standard consumer router doesn't.

## The Hardware

| Device | Role | Approx. Cost |
|---|---|---|
| Unifi UCG Ultra | Gateway / firewall / controller | ~$129 |
| Unifi USW Lite 8 PoE | Managed switch (powers AP over Ethernet) | ~$109 |
| Unifi U7 Lite | Wi-Fi 7 access point | ~$99 |
| Raspberry Pi 4 (2GB) | Pi-hole DNS server | ~$45–$80 |
| MicroSD card + case | Pi-hole storage | ~$15–$25 |

**Total estimated cost: ~$397–$442**

## Why Unifi

Unifi (made by Ubiquiti) is the sweet spot between consumer gear and true enterprise equipment. It is what many small businesses and schools actually run. The key difference from a consumer router is that the gateway, switch, and access point are all managed from a single interface — the Unifi Network application — which runs directly on the UCG Ultra. There is no cloud subscription required and no monthly fee.

What that unified management buys you in practice:

- **Per-device traffic visibility** — you can see exactly what every device on the network is doing, in real time
- **VLANs** — separate your IoT devices, guest network, and personal computers so they can't talk to each other even when on the same physical network
- **Firewall rules** — block traffic between VLANs, restrict outbound connections, or isolate a device entirely, all from a clean UI
- **Intrusion Detection & Prevention (IDS/IPS)** — the UCG Ultra includes threat detection that flags known malicious traffic patterns
- **Automatic updates** — firmware updates across all devices from one place

## The UCG Ultra — Gateway and Brain

The UCG Ultra is the centerpiece. It replaces the router your ISP provided (which you should put in bridge mode or bypass entirely). Beyond basic routing, it runs the full Unifi Network controller software on-device, so there is no need for a separate server or a cloud account to manage everything. Features that matter most for home security:

- **Stateful firewall** with customizable rules — not just port forwarding, but real inbound and outbound policy control
- **Threat Management** — built-in IDS/IPS that uses Emerging Threats signatures to detect and block known bad actors
- **Traffic statistics** — per-client bandwidth and DNS lookups over time
- **VPN server** — connect securely back to your home network from anywhere

## The USW Lite 8 PoE — Managed Switch

A managed switch is what separates a proper network from a home setup where everything is plugged into a cheap unmanaged switch or daisy-chained off the router. The USW Lite 8 PoE has two important roles here:

- **Powers the access point** — the U7 Lite gets its power over the Ethernet cable (PoE), so no separate power adapter is needed near the ceiling where the AP is mounted
- **VLAN tagging** — the switch enforces which network each physical port belongs to, so a device plugged in with a cable can be isolated to the IoT VLAN just as easily as a wireless device

## The U7 Lite — Wi-Fi 7 Access Point

The U7 Lite is a ceiling-mount Wi-Fi 7 access point. Compared to the antenna in a consumer combo router, a dedicated AP gives better coverage, more consistent speeds, and cleaner separation between the radio hardware and the routing/firewall logic.

Because it is managed by the UCG Ultra, you get one consistent SSID experience across the whole house — devices roam seamlessly between bands without you managing it. The access point also enforces VLAN assignments for wireless clients, so your IoT SSID is truly isolated at the network level, not just a separate password.

## Pi-hole — DNS-Level Ad and Threat Blocking

Pi-hole is a DNS sinkhole that runs on a Raspberry Pi. Every DNS query from every device on your network passes through it before reaching the internet. When a device tries to resolve a domain that is on a blocklist — ad networks, known malware domains, telemetry endpoints, tracking services — Pi-hole returns nothing. The request never leaves your network.

This is fundamentally different from browser-based ad blockers:

- It works on **every device** — smart TVs, phones, gaming consoles, IoT devices — not just browsers where you can install an extension
- It blocks at the **network level**, before the connection is made, not after the content is downloaded
- It gives you a **full query log** — you can see every domain every device on your network has tried to reach, which is excellent for spotting misbehaving devices

To integrate Pi-hole with Unifi, you set the Pi-hole's static IP as the DNS server in the UCG Ultra's DHCP settings. Every client on every VLAN then automatically uses Pi-hole for DNS without any per-device configuration.

<div class="note">
  Pi-hole also acts as a local DNS server, meaning you can give your devices memorable hostnames (like <code>nas.home</code> or <code>pi.home</code>) that resolve inside your network without touching the public internet.
</div>

## The VLANs I Run

Network segmentation is the single most impactful security decision in this setup. Even if one device gets compromised, it can only reach other devices on the same VLAN — not your computers, not your NAS, not your router's admin interface. My three-VLAN layout:

- **Main** — trusted computers and phones. Full internet access, can reach the Pi-hole admin UI.
- **IoT** — smart TVs, light bulbs, thermostats, cameras. Internet access only; blocked from reaching any device on Main.
- **Guest** — a clean SSID for visitors. Internet only, isolated from everything else, bandwidth-limited.

## What This Gives You That a Consumer Router Doesn't

- Real-time visibility into every device and every DNS query on your network
- Actual firewall rules, not just NAT and port forwarding
- Network-wide ad and malware domain blocking, including on devices that can't run blockers themselves
- VLAN isolation so a compromised smart bulb can't probe your laptop
- IDS/IPS that flags known threat signatures passing through the gateway
- A setup you actually understand well enough to troubleshoot and extend

## Was It Worth It?

Yes. The combination of Unifi's unified management and Pi-hole's DNS visibility gives me a level of awareness and control over my home network that I didn't have before. More importantly, I understand every piece of it — how the VLANs are enforced, why the Pi-hole is authoritative for DNS, what the IDS is watching for. That understanding is the point. Security tooling you don't understand doesn't actually make you more secure; it just gives you a false sense of it.

Under $500, no monthly fees, no vendor cloud account required, and a network I can honestly say I know inside and out.
