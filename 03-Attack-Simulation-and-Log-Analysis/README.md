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

### Failed SSH Authentication Attempts

From Kali Linux, I simulated unauthorized SSH login attempts against the Ubuntu-SIEM server using invalid usernames and incorrect passwords.

![Failed SSH Attempts from Kali](kali%20failed-invalid%20ssh%20attempts.png)

These attempts represent suspicious authentication activity that a security analyst could investigate through the target system's authentication logs.

## Linux Authentication Log Analysis

On Ubuntu-SIEM, I investigated SSH authentication events using `journalctl`.

Example:

```bash
sudo journalctl -u ssh --no-pager
```

I filtered the logs to identify events such as:

![Ubuntu journalctl showing failed password for invalid user](ubuntu%20journalctl%20failed%20password%20for%20invalid%20user.png)

- `Invalid user`
- `Failed password`
- `Accepted password`
- SSH session creation
- Session termination

This allowed me to connect activity generated from Kali Linux with the corresponding authentication records on Ubuntu.

### Successful SSH Authentication

After analyzing failed authentication attempts, I performed a successful SSH login from Kali Linux to the Ubuntu-SIEM server using the authorized `siemadmin` account.

![Successful SSH Login from Kali](kali%20success%20ssh.png)

On Ubuntu-SIEM, I verified the successful authentication event in the system logs. The `Accepted password` event confirmed that the `siemadmin` account successfully authenticated through SSH.

![Ubuntu Log Showing Accepted Password](ubuntu%20log%20showing%20accepted%20password.png)

This demonstrated the difference between failed authentication attempts and a successful login and showed how both activities can be correlated between the source system and the target's authentication logs.

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
### Privilege Escalation Evidence

The `siemadmin` account used `sudo -i` to obtain an interactive root shell.

![Privilege Escalation from siemadmin to root](privilege%20escalation%20from%20siemadmin%20to%20root.png)

Reviewing the authentication logs showed the sudo activity and the opening of the privileged root session.

![Logs Showing Sudo Root Session](logs%20showing%20sudo-root%20session.png)

This was important because a successful SSH login alone does not prove privilege escalation. Correlating the SSH authentication with the subsequent sudo activity showed the progression from remote access as `siemadmin` to elevated access as `root`.

This demonstrated an important security concept: a successful login does not necessarily represent the end of an investigation.

An analyst should also examine what the authenticated user did after gaining access.

## Event Correlation

Instead of analyzing individual log entries in isolation, I practiced connecting multiple events together.

A possible sequence was:

```text
Failed SSH attempts
        ↓
Accepted password for siemadmin
        ↓
SSH session opened
        ↓
sudo -i
        ↓
siemadmin → root
        ↓
Root session opened
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
