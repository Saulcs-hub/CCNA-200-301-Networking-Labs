# Lab 03 — Secure Cisco Switch Administration

**Cisco CCNA 200-301 | Cisco Packet Tracer | Network Security Fundamentals**

## 1. Project Overview

This laboratory demonstrates the initial configuration and basic administrative security of a Cisco Catalyst 2960 switch using Cisco IOS.

The objective was to configure a new network device, restrict administrative access, implement console authentication, display an authorized-access warning, and ensure that the configuration persists after a system reboot.

The lab was completed using a direct console connection between an administrator PC and a Cisco switch.

## 2. Network Topology

![Network Topology](evidence/01-topology.png)

### Devices Used

| Device | Model | Purpose |
|---|---|---|
| PC-ADMIN | Packet Tracer PC | Administrative workstation |
| SW-CORE | Cisco Catalyst 2960 | Managed network switch |
| Console Cable | Rollover | Local administrative connection |

**Connection:** PC-ADMIN RS-232 → SW-CORE Console

No IPv4 address was required because the switch was accessed through a local console connection rather than through the network.

## 3. Lab Objectives

- Access Cisco IOS through a console connection.
- Navigate between Cisco IOS operating modes.
- Configure a device hostname.
- Protect Privileged EXEC access using `enable secret`.
- Configure console authentication using `password` and `login`.
- Enable password obfuscation.
- Configure a Message of the Day (MOTD) banner.
- Configure an inactivity timeout.
- Save the running configuration to NVRAM.
- Verify authentication and configuration persistence after a reboot.

## 4. Cisco IOS Configuration

### 4.1 Device Hostname

The default hostname was changed to identify the device.

```text
enable
configure terminal
hostname SW-CORE
```

**Result:** The command prompt changed to `SW-CORE(config)#`.

### 4.2 Privileged EXEC Protection

A secret was configured to restrict access to Privileged EXEC mode.

```text
enable secret <LAB_SECRET>
```

This provides an additional authentication step when transitioning from User EXEC mode (`>`) to Privileged EXEC mode (`#`).

The Packet Tracer device stored the enable secret using a Type 5 hash.

### 4.3 Console Authentication

The console line was configured to require a password.

```text
line console 0
password <LAB_CONSOLE_PASSWORD>
login
```

The `password` command defines the console password, while `login` enables password verification for console access.

### 4.4 Console Session Timeout

An inactivity timeout was configured.

```text
line console 0
exec-timeout 5 0
```

After five minutes of inactivity, Cisco IOS terminates the EXEC session.

This helps reduce the risk associated with unattended administrative sessions.

### 4.5 Password Obfuscation

The following command was configured:

```text
service password-encryption
```

In this lab, the console password was displayed in the running configuration as a Type 7 password.

**Security note:** Cisco Type 7 password obfuscation is reversible and should not be considered strong encryption. Modern administrative security requires stronger authentication and secure management protocols.

### 4.6 MOTD Security Banner

A warning banner was configured:

```text
banner motd #AUTHORIZED PERSONNEL ONLY - NETWORK ACCESS RESTRICTED#
```

The message is displayed during console access to warn that the device is restricted to authorized personnel.

## 5. Configuration Verification

After applying the configuration, I used the following commands:

```text
show running-config
show startup-config
```

The running configuration confirmed the presence of the hostname, password protection settings, console authentication, MOTD banner, and inactivity timeout.

Sensitive password values and hashes have been omitted from this documentation.

### Console Access Verification

![Console Login](evidence/05-console-login.png)

After reconnecting to the console, Cisco IOS displayed the security banner and requested the console password.

Successful authentication granted access to User EXEC mode.

### Privileged EXEC Verification

![Privileged Access](evidence/08-privileged-access.png)

The `enable` command required an additional secret before access to Privileged EXEC mode was granted.

This demonstrated separation between initial console authentication and privileged administrative access.

## 6. Saving the Configuration

The active configuration was saved using:

```text
copy running-config startup-config
```

Cisco IOS displayed `[OK]`, confirming that the configuration was saved successfully.

The running configuration resides in RAM, while the startup configuration is stored in NVRAM and is normally loaded during device startup.

## 7. Reboot and Persistence Testing

To verify configuration persistence, the switch was restarted using:

```text
reload
```

![Reload Verification](evidence/07-reload-verification.png)

After the restart, the following settings remained operational:

| Verification | Result |
|---|---|
| Device hostname | Passed |
| MOTD security banner | Passed |
| Console password authentication | Passed |
| Privileged EXEC authentication | Passed |
| Startup configuration persistence | Passed |
| Console inactivity timeout configuration | Verified |

The results demonstrate that the previously saved configuration was successfully restored during startup.

## 8. Troubleshooting and Technical Observations

### Console Password Without Login

During the configuration process, I learned that defining a console password alone is not sufficient to activate line password authentication.

For traditional line-password authentication, both commands are required:

```text
password <LAB_CONSOLE_PASSWORD>
login
```

### Cisco IOS Command Modes

I also practiced identifying the correct mode for each configuration command.

| Cisco IOS Prompt | Operating Mode |
|---|---|
| `SW-CORE>` | User EXEC |
| `SW-CORE#` | Privileged EXEC |
| `SW-CORE(config)#` | Global Configuration |
| `SW-CORE(config-line)#` | Line Configuration |

Understanding command modes helps prevent configuration errors and improves troubleshooting efficiency.

## 9. Key Lessons Learned

Through this laboratory, I developed practical skills in:

1. Configuring a Cisco switch through a direct console connection.
2
