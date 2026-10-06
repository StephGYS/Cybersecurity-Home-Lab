# Phase 4 — Security Monitoring & Auditd

## Objective

The objective of this phase is to move beyond manually reviewing individual
authentication logs and begin monitoring system activity using security
monitoring and Linux auditing tools.

In this phase, I worked with Wazuh and Linux Audit (auditd) on my Ubuntu-SIEM
system to understand how host activity can be collected, monitored, and
investigated.

The goal is to understand not only that an event occurred, but also which user
initiated the activity, what privilege level was used, what executable was
involved, and how the event can be reconstructed during an investigation.

## Lab Environment

- **Kali Linux:** Simulated attacker/testing system
- **Ubuntu-SIEM:** Monitored endpoint
- **Wazuh:** Security monitoring and event analysis
- **auditd:** Linux auditing framework
- **pfSense:** Network gateway/firewall
- **Network:** Isolated cybersecurity home lab

## Linux Audit Configuration

During this phase, I configured and troubleshot Linux Audit on Ubuntu-SIEM.

I verified that the audit service was active and generated activity that could
be investigated through the Linux audit logs.

### Auditd Service Verification

I verified that the `auditd` service was active and running on Ubuntu-SIEM.

![Auditd service active](auditd-service-activee.png)

The purpose of using auditd was to gain more visibility into system activity
than authentication logs alone can provide.

## Audit Event Investigation

I used audit search tools to investigate recorded events.

One of the events contained information such as:

```text
exe=/usr/sbin/ausearch
uid=0
auid=1000
key=command_execution
