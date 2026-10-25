# Lab 03 — Secure Cisco Switch Administration Evidence

**Cisco CCNA 200-301 | Cisco Packet Tracer**

## Overview

This folder contains screenshots documenting the initial administration and basic security configuration of a Cisco Catalyst 2960 switch.

The laboratory demonstrates local console access, Cisco IOS configuration, password authentication, a security banner, configuration verification, and persistence testing after a device restart.

---

## 1. Network Topology

![Lab 03 Network Topology](01-Topologia_LAB03.png)

**Description:** A PC named PC-ADMIN connects directly to the console port of a Cisco Catalyst 2960 switch named SW-CORE using a rollover cable.

This connection provides out-of-band local administrative access without requiring an IPv4 address.

## 2. Initial Device Configuration

This stage documents the initial Cisco IOS configuration through the console connection.

**Actions performed:**
- Accessed Cisco IOS.
- Entered Privileged EXEC mode.
- Entered Global Configuration mode.
- Changed the hostname to SW-CORE.
- Began configuring administrative access controls.

*Evidence: `02-Configuracion_inical_LAB03...` — use the exact filename uploaded to GitHub.*

## 3. Console Authentication

This stage documents the configuration of the console line.

**Cisco IOS commands:**

```text
line console 0
password <LAB_CONSOLE_PASSWORD>
login
exec-timeout 5 0
```

The `password` and `login` commands enable traditional console password authentication.

The inactivity timeout terminates an inactive EXEC session after five minutes.

*Evidence: `03-Configuracion_Conconsola-...` — use the exact filename uploaded to GitHub.*

## 4. Running Configuration Verification

![Configuration Verification](05-Mostrando_conf.png)

The running configuration was inspected to verify the administrative configuration applied to the switch.

The configuration included the SW-CORE hostname, access restrictions, and password obfuscation settings.

## 5. Cisco IOS Configuration Inspection

![Cisco IOS Configuration](06-MostrandoConf.png)

The configuration output was reviewed to understand how Cisco IOS stores administrative settings.

Sensitive password values and hashes should be hidden before publishing screenshots in a public repository.

## 6. Console Security and Timeout Verification

![Complete Configuration](07-conf-completa.png)

The console configuration was inspected to verify:

- Console password authentication.
- The `login` command.
- The five-minute `exec-timeout`.
- The configured security banner.

## 7. Startup Configuration and Persistence

The running configuration was saved to NVRAM using:

```text
copy running-config startup-config
```

The switch returned `[OK]`, indicating that the save operation completed.

A subsequent reboot was performed, and the previously configured banner, hostname, and authentication requirements remained operational.

The configuration persistence test was successfully demonstrated during the laboratory.

## 8. Verification Summary

| Verification | Result |
|---|---|
| Console connection established | Passed |
| Device hostname configured | Passed |
| Console authentication | Passed |
| Privileged EXEC authentication | Passed |
| MOTD security banner | Passed |
| Console inactivity timeout configuration | Verified |
| Configuration saved to NVRAM | Passed |
| Authentication after restart | Passed |

## 9. Key Lessons Learned

Through this laboratory, I practiced configuring a Cisco switch using a console connection and verifying basic administrative security controls.

I learned how different Cisco IOS command modes affect configuration, why console authentication requires both `password` and `login`, and how `enable secret` protects Privileged EXEC access.

I also verified the difference between the running configuration stored in RAM and the startup configuration stored in NVRAM.

The final reboot test demonstrated the importance of saving configurations before restarting a network device.

---

**Tools:** Cisco Packet Tracer  
**Certification:** Cisco CCNA 200-301  
**Study Reference:** Jeremy's IT Lab — Cisco IOS CLI and Basic Device Configuration
