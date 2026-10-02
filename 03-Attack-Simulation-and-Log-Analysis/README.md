# Phase 3 — Attack Simulation & Log Analysis

## Objective

The objective of this phase was to understand how suspicious authentication activity appears in Linux logs and how a security analyst can investigate that activity.

Using Kali Linux as the simulated attacker and Ubuntu-SIEM as the target system, I generated SSH authentication events and investigated the resulting logs.

All activity was performed inside my isolated cybersecurity home lab.

## Lab Environment

- **Kali Linux:** Simulated attacker
- **Ubuntu-SIEM:** Target system
- **Wazuh:** Security monitoring and log analysis
- **pfSense:** Network gateway/firewall
- **Network:** `10.0.5.0/24`

## Attack Simulation

From Kali Linux, I generated SSH authentication activity against the Ubuntu-SIEM server.

The exercises included:

- SSH connection attempts
- Invalid usernames
- Incorrect passwords
- Successful SSH authentication
- Privilege escalation using `sudo`
- Root shell activity

These actions generated authentication events that could be investigated from the defensive side.

## Linux Authentication Log Analysis

On Ubuntu-SIEM, I investigated SSH authentication events using `journalctl`.

Example:

```bash
sudo journalctl -u ssh --no-pager
```

I filtered the logs to identify events such as:

- `Invalid user`
- `Failed password`
- `Accepted password`
- SSH session creation
- Session termination

This allowed me to connect activity generated from Kali Linux with the corresponding authentication records on Ubuntu.

## Source IP Investigation

The SSH logs allowed me to identify the source IP address responsible for authentication attempts.

By correlating the source IP, username, timestamp, and authentication result, I could reconstruct what happened during the simulated activity.

This demonstrated how authentication logs can help distinguish normal login activity from potentially suspicious behavior.

## Privilege Escalation Investigation

After successful SSH authentication, I investigated privilege escalation activity.

The account:

```text
siemadmin
```

used `sudo` to obtain elevated privileges.

Example:

```bash
sudo -i
```

The logs showed activity involving:

```text
siemadmin → root
```

This demonstrated an important security concept: a successful login does not necessarily represent the end of an investigation.

An analyst should also examine what the authenticated user did after gaining access.

## Event Correlation

Instead of analyzing individual log entries in isolation, I practiced connecting multiple events together.

A possible sequence was:

```text
SSH authentication attempts
        ↓
Failed authentication
        ↓
Successful authentication
        ↓
Session opened
        ↓
sudo execution
        ↓
Privilege escalation
        ↓
Root session
```

Correlating these events provides more context than examining a single authentication event.

## Security Analysis

A failed SSH login alone does not automatically indicate an attack. A legitimate user may simply enter an incorrect password.

However, repeated failures, invalid usernames, successful authentication, and subsequent privilege escalation can become more significant when they occur as part of the same sequence.

This phase taught me to investigate the complete chain of activity rather than relying on a single event.

## What I Learned

Through this phase, I practiced:

- Generating controlled SSH authentication activity
- Reading Linux authentication logs
- Identifying source IP addresses
- Identifying invalid-user attempts
- Distinguishing failed and successful authentication
- Tracking SSH sessions
- Investigating `sudo` activity
- Identifying privilege escalation from a standard user to root
- Correlating multiple events into an investigation timeline
- Understanding the difference between an individual event and a suspicious sequence of events

## Connection to Cybersecurity

This exercise demonstrated the relationship between offensive activity and defensive monitoring.

Kali Linux generated the activity, Ubuntu recorded the events, and the monitoring environment provided evidence that could be analyzed.

The main lesson was:

> A security analyst should not stop at "Was the login successful?" The analyst should determine where the connection came from, which account was used, what happened after authentication, and whether privileges were elevated.
