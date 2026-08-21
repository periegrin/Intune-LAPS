# Troubleshooting and Lessons Learned

## Issue: LAPS Affected Devices Outside the Test Group

### Symptoms

The Windows LAPS policy was assigned to a specific test group in Microsoft Intune.

However, LAPS appeared to be affecting devices outside the intended test population, despite the Intune policy assignment being configured specifically for the selected test devices.

### Initial Assumption

The first step was to validate the Microsoft Intune assignment scope. The assignment appeared correct, so the investigation expanded to consider whether other configuration mechanisms might be affecting the devices.

## Root Cause: Conflicting Group Policy Configuration

During the investigation, we discovered an existing Group Policy Object (GPO) that was already configuring Windows LAPS.

This created two independent management paths for LAPS:

```text
Group Policy
     |
     +---- Windows LAPS

Microsoft Intune
     |
     +---- Windows LAPS
```

**The root cause was not the Intune assignment itself.** The existing Group Policy configuration was applying LAPS independently, which explained why device scope did not match the Intune assignment.

## Resolution

The existing LAPS GPO configuration was disabled as part of the migration and testing process.

After removing the conflicting GPO, the Intune LAPS policy behaved according to the expected assignment scope, allowing us to target LAPS only to the intended test devices.

## Verification After the Fix

For details on how the configuration was verified through Intune and Microsoft Entra ID, see [Testing and Validation — Verification](testing.md#verification).

The verification confirmed that:

- The Intune policy was applied only to intended test devices
- Windows LAPS successfully managed the local administrator account
- Password backup to Microsoft Entra ID was working as expected

## Lesson Learned: Multiple Management Paths

One of the most important lessons from this implementation was that **validating an Intune assignment is only one part of endpoint troubleshooting.**

In an enterprise environment, a device can be affected by multiple management mechanisms:

- Existing Group Policy configuration
- Existing Intune configuration profiles
- Security baselines
- PowerShell scripts
- Configuration Manager
- Other endpoint management tools
- Existing Windows configuration

A policy can be correctly assigned in Intune and still produce unexpected behavior if another management mechanism is configuring the same Windows feature.

**Key principle:** A correctly assigned Intune policy does not necessarily mean it is the only configuration affecting a device. Understanding the complete management state of an endpoint is essential when troubleshooting unexpected behavior.

## Migration Checklist: Moving LAPS from Group Policy to Intune

Moving Windows LAPS management from Group Policy to Intune should be treated as a **configuration migration**, not simply a new policy deployment.

Before broad deployment:

1. Identify existing Windows LAPS configuration (Group Policy, Intune profiles, scripts, etc.)
2. Determine which devices and users are affected by existing configuration
3. Establish which management platform will become authoritative
4. Identify and disable or retire all conflicting configuration
5. Test the new Intune configuration on a controlled device group
6. Validate local administrator account management
7. Validate password rotation
8. Validate password backup
9. Validate authorized password retrieval
10. Move to pilot deployment (see [Testing and Validation](testing.md))
11. Monitor the pilot population
12. Proceed to production after successful validation

This checklist reflects the actual process followed in this implementation, where step 4 (disabling the existing GPO) was critical to ensuring the migration succeeded.

## Practical Takeaway

The most valuable aspect of this project was not simply enabling Windows LAPS—it was identifying how LAPS interacted with existing endpoint management systems and resolving the conflict before production rollout.

This experience reinforced the importance of:

- Understanding the complete management state of an endpoint before deployment
- Treating major policy migrations as comprehensive projects, not isolated policy changes
- Using controlled testing to identify configuration conflicts early
- Documenting findings to guide future migrations

For implementation details and the staged testing approach, see [Windows LAPS Implementation](implementation.md) and [Testing and Validation](testing.md).

---

*Documenting real endpoint engineering work and lessons learned.*
