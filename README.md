# Intune-LAPS

A real-world implementation of Windows LAPS using Microsoft Intune and Microsoft Entra ID.

## Why I Implemented This

Before implementing Windows LAPS, our IT team was maintaining local administrator passwords in an Excel spreadsheet.

The spreadsheet contained the local administrator password for each Windows device.

While this gave the IT team a way to recover local administrator credentials, it also created a security and operational concern. Passwords were being managed manually, and maintaining a reliable record became increasingly difficult as the number of devices grew.

The goal was to move away from manually maintaining local administrator credentials and have Windows manage the account and password automatically.

The solution was Windows LAPS, managed through Microsoft Intune with password backup to Microsoft Entra ID.

---

## Implementation Approach

We used a staged deployment rather than immediately applying the configuration to all devices.

The rollout was:

**Selected IT devices → Pilot groups → Production**

The initial deployment was made to selected IT devices so we could validate the configuration and collect feedback.

After the initial testing, the configuration was moved to pilot groups before production deployment.

This staged approach allowed us to identify and resolve an existing configuration issue before the solution was introduced more broadly.

---

## Configuration

The implementation uses Windows LAPS policy settings to manage the local administrator account, rotate the password automatically, and back up the password to Microsoft Entra ID.

For security reasons, the exact production values and internal naming conventions are not published in this repository.

The implementation includes the following capabilities:

| Setting | Implementation |
| --- | --- |
| Password Backup | Microsoft Entra ID |
| Password Rotation | Configured according to organizational requirements |
| Password Complexity | Configured according to organizational requirements |
| Password Length | Configured according to organizational requirements |
| Post-Authentication Actions | Enabled |
| Automatic Account Management | Enabled |
| Account Name Randomization | Enabled |
| Account Name Prefix | Redacted |
| Target Account | Built-in Administrator |
| Account Management | Enabled |

The values shown in screenshots and examples in this repository are sanitized where necessary and should not be interpreted as the organization's production security baseline.

---

## Account Name Randomization

Automatic account management and account-name randomization are supported on Windows 11 version 24H2 and later.

When account-name randomization is enabled, Windows LAPS generates a randomized account name using the configured naming convention.

The randomized account name receives a six-digit numeric suffix.

For public documentation, the actual account naming convention has been redacted.

Example:

```text
<REDACTED_PREFIX>_XXXXXX
