---
title: "[TIL] Routing"
author: "Wook Lee"
date: "2024-06-18"
tags: ["Today I Learned"]
---

I'm currently studying for the CCNA exam, primarily using Jeremy's IT Lab YouTube channel. He's well-known for providing great CCNA content, and the best part is that it's all free on YouTube. Jeremy also offers Anki flashcards, which I absolutely love and use daily, as well as Cisco Packet Tracer, which is an excellent resource for hands-on practice. I plan to take the exam in 3 to 4 months, though this timeline might change depending on my study and preparation progress.

Today, I learned about routing.

### What is Routing?

---

Definition:

> Routing is the process that routers use to determine the path that IP packets should take over a network to reach their destination.

When a router receives a packet, it's the router's job to forward it to the correct destination.

Routers store routes to all of their known destinations in a **Routing table**.

Switches keep a MAC address table with their known destination MAC addresses, and routers keep a routing table with their known destination networks.

When routers receive packets, they look in the routing table to find the best route to forward the packet.

There are two main routing methods:

- **dynamic routing**: Routers use dynamic protocols (i.e., OSPF) to share routing information with each other automatically and build their routing tables.

- **static routing**: A network engineer/admin manually configures routes on the router.
  But before we go into routing methods,

### What exactly is a route?

---

Basically, it's an instruction to the router.

A route tells the router:

- To send a packet to destination X, you should send the packet to next-hop Y.
- or, if the destination is directly connected to the router, send the packet directly to the destination.
- or, if the destination is the router's own IP address, receive the packet for yourself (don't forward it).

Also, there are few types of routes found in routing tables.

1. **Connected routes**
2. **Local routes**

**Connected Routes**

- These are routes that are directly connected to a router's interface.
- they are automatically created when an interface is configured with an IP address and brought up. Connected routes are essential for a router to communicate with devices on the directly attached network.

**Local Routes**

- These are routes to the IP addresses assigned to the router's own interfaces.
- Local routes are used to specify that the router can directly reach its own IP addresses. They have the highest priority in the routing table since the router must be able to communicate with its own interfaces.
- **/32** netmask is used to specify the exact IP address of the interface.

There's a special kind of static route called a **default route**.

This is a route that you configure inside the router that says, if nothing else matches inside of our routing table, send the traffic this way.

It's also known as the "gateway of last resort"

A default route is a route to **0.0.0.0/0**

- **/0** means that all bits of the netmask are 0, so none of the bits of the addresses are fixed. They can all change to be either 0 or 1.
- **0.0.0.0/0** is the least specific route possible; it includes every possible destination IP address.
- If the router doesn't have any more specific routes that match a packet's destination IP address, the router will forward the packet using the default route.

* A default route is often used to direct traffic to the Internet.
  - More specific routes are used for destinations in the internal corporate network.
  - Traffic to destinations outside of the internal network is sent to the Internet.
