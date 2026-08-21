# Intune-LAPS

A real-world implementation of Windows LAPS using Microsoft Intune and Microsoft Entra ID.

## Why I Implemented This

Before implementing Windows LAPS, our IT team was maintaining local administrator passwords in an Excel spreadsheet.

The spreadsheet contained the local administrator password for each Windows device.

While this gave the IT team a way to recover local administrator credentials, it also created a security and operational concern. Passwords were being managed manually, and maintaining a reliable record became increasingly difficult as the number of devices grew.

The goal was to move away from manually maintaining local administrator credentials and have Windows manage the account and password automatically.

The solution was Windows LAPS, managed through Microsoft Intune with password backup to Microsoft Entra ID.

## Implementation Approach

We used a staged deployment rather than immediately applying the configuration to all devices.

The rollout was:

**Selected IT devices → Pilot groups → Production**

The initial deployment was made to selected IT devices so we could validate the configuration and collect feedback.

After the initial testing, the configuration was moved to pilot groups before production deployment.

This staged approach allowed us to identify and resolve an existing configuration issue before the solution was introduced more broadly.

## Configuration

The implementation used Windows LAPS policy settings to manage the local administrator account, rotate the password automatically, and back up the password to Microsoft Entra ID.

For security reasons, the exact production values and internal naming conventions are not published in this repository.

The implementation included the following capabilities:

| Setting                      | Implementation                                      |
| ---------------------------- | --------------------------------------------------- |
| Password Backup              | Microsoft Entra ID                                  |
| Password Rotation            | Configured according to organizational requirements |
| Password Complexity          | Configured according to organizational requirements |
| Password Length              | Configured according to organizational requirements |
| Post-Authentication Actions  | Enabled                                             |
| Automatic Account Management | Enabled                                             |
| Account Name Randomization   | Enabled                                             |
| Account Name Prefix          | Redacted                                            |
| Target Account               | Built-in Administrator                              |
| Account Management           | Enabled                                             |

The values shown in screenshots and examples in this repository are sanitized where necessary and should not be interpreted as the organization's production security baseline.

### Account Name Randomization

Automatic account management and account-name randomization are supported on Windows 11 version 24H2 and later.

When account-name randomization is enabled, Windows LAPS generates a randomized account name based on the configured naming convention.

For public documentation, the actual account name has been redacted:

```text
XXXXXX
```

The actual account name, password, device identifiers, security identifiers, and internal naming conventions are intentionally not published.

## The Problem We Found During Testing

The initial testing produced an unexpected result.

We had enabled the required Microsoft Entra LAPS configuration and assigned the Intune policy to a specific group of test devices.

However, we noticed that LAPS appeared to be affecting a much larger group of devices, including devices that were not part of the intended test group.

The Intune assignment itself appeared to be correct, so we investigated the existing endpoint configuration.

## Root Cause

During the investigation, we discovered that the environment already had an existing Group Policy Object (GPO) that was also configuring Windows LAPS.

This meant that we were introducing an Intune-based LAPS configuration into an environment where LAPS was already being configured through Group Policy.

The existing GPO was the reason our initial testing did not behave as expected.

## Resolution

We disabled the existing LAPS GPO and repeated the test.

After removing the existing Group Policy configuration, the LAPS policy behaved as expected.

We were then able to target the Intune LAPS configuration to the specific devices we wanted to test.

This allowed us to continue with the pilot rollout.

## Verification

We verified the implementation through both Microsoft Intune and Microsoft Entra ID.

From Intune, we verified the device's **Local admin password** information and confirmed that Windows LAPS had successfully created and managed the account.

We also verified the password rotation information and confirmed that the LAPS password was available for authorized retrieval.

The actual account name and password are not included in this repository.

We then verified the LAPS information through Microsoft Entra ID.

This allowed us to validate the complete management flow:

```text
Microsoft Intune
       |
       | LAPS Policy
       v
Windows Device
       |
       | Windows LAPS
       v
Local Administrator Account
       |
       | Password Backup
       v
Microsoft Entra ID
```

## What I Learned

The biggest lesson from this implementation was that validating an Intune assignment is only part of the deployment process.

When introducing an Intune-based configuration into an existing enterprise environment, it is important to understand what other management mechanisms may already be configuring the device.

In this case, the Intune assignment was correct.

The unexpected behavior was caused by an existing LAPS configuration through Group Policy.

Removing the conflicting configuration allowed us to move to a cleaner Intune-managed deployment model.

The staged rollout also helped us identify the issue before the configuration was broadly deployed.

## Security and Privacy

This repository intentionally does not contain:

* Local administrator passwords
* Password fragments
* Device names
* Security identifiers
* Tenant identifiers
* User credentials
* Access tokens
* Client secrets
* Internal policy names
* Internal assignment group names
* Internal URLs
* Company-specific security configuration

Screenshots are sanitized before publication.

Production security settings may be represented using generalized or modified values where necessary.

## Project Status

This project documents the implementation approach, deployment experience, troubleshooting, and lessons learned from implementing Windows LAPS with Microsoft Intune and Microsoft Entra ID.

Additional documentation and sanitized examples will be added as the project develops.

## References

* Microsoft Learn — Windows LAPS
* Microsoft Learn — Windows LAPS Policy Settings
* Microsoft Learn — Deploy Intune Policies to Manage Windows LAPS
* Microsoft Learn — Windows LAPS with Microsoft Entra ID

## Author

**Periegrin Develos**

IT Team Manager | Microsoft Intune & SCCM Specialist | Endpoint & EUC Management | PowerShell Automation | Windows | Microsoft Graph

[LinkedIn](https://www.linkedin.com/in/periegrin-develos-b69ba5101/)

---

*Documenting real endpoint engineering work and lessons learned.*
