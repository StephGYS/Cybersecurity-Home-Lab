# Phase 1 — Cybersecurity Home Lab Setup

## 🎯 Objective

The objective of this phase was to build an isolated virtual cybersecurity laboratory where I could safely practice networking, Linux administration, security monitoring, attack simulation, and defensive security techniques.

## 🖥️ Lab Components

The environment was built using VirtualBox and several virtual machines:

- **pfSense** — Firewall and gateway
- **Kali Linux** — Security testing / attacker workstation
- **Ubuntu Linux** — Target server and monitored system

## 🌐 Network Architecture

The virtual machines communicate through an isolated lab network.

Basic architecture:

![Cybersecurity Home Lab Network Architecture](home-lab-network-diagram.png.png)

pfSense acts as the gateway between the virtual systems and allows me to practice network segmentation, routing, firewall concepts, and traffic monitoring.

## 🔐 Why an Isolated Lab?

Using a dedicated virtual environment allows security testing to be performed without intentionally targeting external systems.

This gives me a controlled environment where I can generate security events and then investigate them from both offensive and defensive perspectives.

## 🧪 Skills Practiced

During this phase I practiced:

- Creating and configuring virtual machines
- Understanding virtual network adapters
- Configuring an internal lab network
- IP addressing
- Network gateways
- Basic routing
- Network segmentation
- pfSense configuration
- Testing connectivity between systems
- Troubleshooting network connectivity

## 📚 Key Takeaway

This phase helped me understand that a cybersecurity lab is not simply a collection of virtual machines. The network architecture determines how systems communicate, what traffic can pass between them, and where security controls such as firewalls can be implemented.
