---
title: "[TIL] Port"
author: "Wook Lee"
date: "2024-06-21"
tags: ["Today I Learned"]
---

I feel like I've only scratched the surface when it comes to understanding Ports. Since I have an interview coming up, I want to review the basics of Ports, even if it's very fundamental.

---

### What is a port?

From Wikipedia

> a port is a number assigned to uniquely identify a connection endpoint and to direct data to a specific service.

To simply put, a port is like an address for a specific program or service running on a computer.

An IP address identifies a device on a network, while a port number identifies a specific process or service running on that device. When data is sent over a network, the IP address directs the data to the correct device, and the port number ensures it reaches the correct application or service on that device.

Because a computer runs multiple processes simultaneously, not just a single one, it is import to distinguish between them. For example, on my computer right now, I have multiple Google Chrome tabs open, Discord running, and I'm listening to music on Spotify.

So, let's say we want to communicate with a program on an external server. Assuming we know its IP address, if we don't know its port number, we won't be able to locate the specific program we want to communicate with.

### Why do we not specify the port numbers in the browser?

It is said that to communicate with a specific server, you need to know both the IP address and the port number. Through the domain address, you can only find out the IP address. So, how can we communicate normally without entering the port number?

When you enter a domain address in the address bar and press enter, the browser is set to send the communicate to port 80 by default. That's why you don't need to enter the port number. If you don't want to communicate through port 80, you can set the port number separately in the address bar.

### There are total 65,535 ports.

- Port numbers 0 - 1023 are called `System or Well-known` ports.
- Port numbers 1024 - 49151 are called `User` or `Registered` ports.
- Port numbers 49152-65535 are called `Dynamic` or `Private` ports.

---

Now it all makes sense. When we are doing a CTF, the first thing we always do in the enumeration phase is utilize the `nmap` command to perform port scanning. This helps us identify open ports and see which services are running on them. The target might be running several programs, and we need to find and confirm which programs/services are running and identify corresponding vulnerabilities.
