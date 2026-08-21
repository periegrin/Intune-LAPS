# Troubleshooting and Lessons Learned

## Issue: LAPS Appeared to Affect Devices Outside the Test Group

### Symptoms

The Windows LAPS policy was assigned to a specific test group in Microsoft Intune.

However, during the initial testing, LAPS appeared to be affecting devices outside the intended test population.

This was unexpected because the Intune policy assignment was configured specifically for the selected test devices.

### Initial Assumption

The first area checked was the Microsoft Intune assignment.

The assignment scope appeared correct, so the investigation moved to the existing endpoint configuration to determine whether another configuration was affecting the devices.

## Root Cause

During the investigation, we discovered that an existing Group Policy Object (GPO) was already configuring Windows LAPS.

This meant the environment had two potential management paths for Windows LAPS:

```text
Group Policy
     |
     +---- Windows LAPS

Microsoft Intune
     |
     +---- Windows LAPS
```

The existing GPO was applying LAPS configuration independently of the new Intune policy.

This explained why the initial behavior did not match the expected Intune assignment scope.

The issue was not caused by the Intune assignment itself. The existing Group Policy configuration was also managing LAPS on affected devices.

## Resolution

The existing LAPS GPO configuration was retired as part of the migration and testing process.

The test was then repeated using the same Intune assignment.

After the existing Group Policy LAPS configuration was retired, the Intune LAPS policy behaved according to the expected assignment scope.

We were able to target the LAPS configuration only to the devices intended for testing.

This allowed us to continue with the pilot rollout.

## Verification After the Fix

After resolving the GPO conflict, we performed another validation cycle.

The implementation was verified through both Microsoft Intune and Microsoft Entra ID.

### Microsoft Intune Verification

From Microsoft Intune, we verified the device's **Local admin password** information.

We confirmed that Windows LAPS had successfully:

- Created and managed the local administrator account
- Generated and managed the LAPS password
- Reported the password rotation information
- Made the password available for authorized retrieval

The actual account name and password are intentionally not included in this repository.

### Microsoft Entra ID Verification

We then verified the LAPS information through Microsoft Entra ID.

This confirmed that the password backup process was working as expected.

The complete management flow was validated:

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

This verification confirmed that the Intune policy was being applied to the intended test devices and that Windows LAPS was successfully backing up the managed password to Microsoft Entra ID.

## Testing Approach

The implementation was deployed using a staged approach rather than immediately applying the configuration to the entire environment.

```text
Selected IT Devices
        |
        v
Initial Testing
        |
        v
Pilot Groups
        |
        v
Production
```

### Initial Testing

The first phase used selected IT devices.

The purpose was to:

- Validate the LAPS configuration
- Confirm local administrator account management
- Verify password rotation
- Verify password backup to Microsoft Entra ID
- Identify unexpected behavior
- Collect feedback from the IT team

During this phase, we discovered the existing LAPS GPO.

Because the issue was identified during controlled testing, we were able to investigate and resolve it before moving further into the rollout.

### Pilot Deployment

After the GPO issue was resolved, the Intune LAPS configuration was tested again.

Once the expected behavior was confirmed, the configuration was moved to pilot groups.

The pilot phase provided another opportunity to validate the configuration with a larger but still controlled device population.

### Production

The production rollout was planned only after the configuration had successfully passed the initial IT testing and pilot stages.

This staged approach reduced the risk of introducing an unexpected configuration change across the wider Windows device population.

## Lesson Learned

One of the most important lessons from this implementation was that validating an Intune assignment is only one part of endpoint troubleshooting.

In an enterprise environment, a device can be affected by multiple management mechanisms.

When introducing a new Intune policy, it is important to understand what other configuration mechanisms may already be managing the same Windows feature.

These may include:

- Existing Group Policy configuration
- Existing Intune configuration profiles
- Security baselines
- PowerShell scripts
- Configuration Manager
- Other endpoint management tools
- Existing Windows configuration

A policy can be correctly assigned in Intune and still produce unexpected behavior because another management mechanism is configuring the same Windows feature.

In our case, the Intune assignment was correct.

The unexpected behavior was caused by an existing Windows LAPS configuration through Group Policy.

## Migration Consideration

Moving Windows LAPS management from Group Policy to Intune should be treated as a configuration migration rather than simply creating a new Intune policy.

Before broad deployment, we recommend:

1. Identify existing Windows LAPS configuration.
2. Determine where the existing configuration is being applied.
3. Establish which management platform will become authoritative.
4. Identify and remove or retire conflicting configuration.
5. Test the new configuration on a controlled device group.
6. Validate local administrator account management.
7. Validate password rotation.
8. Validate password backup.
9. Validate authorized password retrieval.
10. Continue through pilot deployment.
11. Monitor the pilot population.
12. Proceed to production after successful validation.

## Security Lesson

Before Windows LAPS was implemented, the IT team maintained local administrator passwords in an Excel spreadsheet.

The spreadsheet provided a way to recover local administrator credentials, but it also meant that sensitive credentials were being maintained manually.

Windows LAPS changed this model by allowing Windows to manage the local administrator password automatically and by providing centralized authorized retrieval through Microsoft Entra ID.

This also removes the dependency on manually maintaining a password record for every device.

The public documentation intentionally does not expose:

- Actual passwords
- Password fragments
- Actual local administrator account names
- Device names
- Device identifiers
- Tenant information
- Security identifiers
- User credentials
- Access tokens
- Client secrets
- Internal policy names
- Internal assignment groups
- Internal URLs
- Company-specific security configuration

Screenshots used in the project are sanitized before publication.

Production configuration values may also be generalized or redacted where publishing the exact value would provide unnecessary information about the organization's environment.

## Practical Takeaway

The most valuable part of this project was not simply enabling Windows LAPS.

It was identifying how LAPS interacted with the existing endpoint management environment and resolving the configuration conflict before production rollout.

The Intune assignment was correct, but the device was also being influenced by an existing Group Policy configuration.

Once the conflicting GPO was removed, the Intune-managed LAPS configuration behaved as expected.

The experience reinforced an important endpoint management principle:

> A correctly assigned Intune policy does not necessarily mean it is the only configuration affecting a device.

Understanding the complete management state of an endpoint is essential when troubleshooting unexpected behavior.

## Final Outcome

After the existing LAPS GPO was disabled, we were able to:

- Target the LAPS policy to the intended test devices
- Validate local administrator account management
- Confirm password rotation
- Confirm password backup to Microsoft Entra ID
- Verify authorized password retrieval
- Continue with the pilot rollout

The implementation provided a practical example of migrating Windows LAPS management into Microsoft Intune within an existing enterprise endpoint environment.

It also demonstrated why controlled testing and understanding existing endpoint configuration are important before introducing a new management platform.

## Summary

The implementation followed this process:

```text
Existing Environment
        |
        v
Identify LAPS Requirement
        |
        v
Configure Windows LAPS in Intune
        |
        v
Selected IT Device Testing
        |
        v
Unexpected Device Scope
        |
        v
Investigate Existing Configuration
        |
        v
Existing LAPS GPO Identified
        |
        v
Disable Conflicting GPO
        |
        v
Retest
        |
        v
Intune Assignment Behaves as Expected
        |
        v
Verify Intune + Entra ID
        |
        v
Pilot Deployment
        |
        v
Production Rollout
```

The main lesson was that endpoint troubleshooting must consider the complete device management state, not only the policy currently being deployed.

---

*Documenting real endpoint engineering work and lessons learned.*
