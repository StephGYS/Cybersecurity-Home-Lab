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

I used `ausearch` on Ubuntu-SIEM to investigate events recorded by Auditd.

One event contained information such as:

```text
exe=/usr/sbin/ausearch
uid=0
auid=1000
key=command_execution
```

### Understanding the Audit Fields

The Auditd event provides several important pieces of information:

- **exe=/usr/sbin/ausearch** — identifies the executable that generated the recorded activity. In this case, the `ausearch` utility was executed.

- **uid=0** — represents the effective user ID associated with the execution. UID `0` corresponds to the `root` account, indicating that the command was executed with root privileges.

- **auid=1000** — represents the Audit User ID. Unlike the effective UID, the AUID tracks the original user who started the login session. This is useful during investigations because it can help identify who initiated an action even after privilege escalation.

- **key=command_execution** — is the custom Auditd rule key used to categorize the event. I configured this key so command execution events could be searched and identified more easily.

Together, these fields show that a command executed with root privileges while Auditd preserved information about the original user associated with the session.


## Wazuh Audit Event Analysis

After investigating the events locally, I used Wazuh to analyze the Auditd events collected from the Ubuntu-SIEM endpoint.

Wazuh converted the raw Linux Audit events into structured security fields, making the activity easier to investigate.

One command execution event showed:

```text
Agent: ubuntu-siem
Agent IP: 10.0.5.10
Executable: /usr/bin/sed
Command: sed
UID: 1000
AUID: 1000
Audit Key: command_execution
Success: yes
Decoder: auditd
```
### Understanding the Wazuh Event

The structured Wazuh event provided additional information about the activity:

- **Agent: ubuntu-siem** — identifies the monitored endpoint that generated the event.

- **Agent IP: 10.0.5.10** — identifies the IP address of the Ubuntu-SIEM endpoint monitored by Wazuh.

- **Executable: /usr/bin/sed** — shows the full path of the program that was executed.

- **Command: sed** — identifies the command that Auditd observed.

- **UID: 1000** — identifies the user ID under which the command executed.

- **AUID: 1000** — identifies the original authenticated user associated with the session.

- **Audit Key: command_execution** — shows that the event matched the Auditd rule I configured to monitor command execution.

- **Success: yes** — indicates that the recorded system call completed successfully.

- **Decoder: auditd** — shows that Wazuh recognized the event as Linux Auditd data and parsed it using its Auditd decoder.

By converting the raw Auditd event into structured fields, Wazuh made it easier to determine what happened, where it happened, which user was involved, and whether the activity was successful.
