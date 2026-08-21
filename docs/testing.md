# Testing and Validation

## Testing Strategy

The Windows LAPS implementation was not deployed directly to production.

Testing followed a staged approach:

``` text
Selected IT Devices
        |
        v
Feedback and Validation
        |
        v
Pilot Groups
        |
        v
Production
```

The first devices were selected from the IT team so that the
implementation could be tested under controlled conditions.

## Initial Test

The initial test included:

1.  Enabling the required Microsoft Entra LAPS configuration.
2.  Creating the Windows LAPS policy in Microsoft Intune.
3.  Assigning the policy to a specific test-device group.
4.  Allowing the selected devices to receive the configuration.
5.  Checking the resulting local administrator account and LAPS status.
6.  Verifying password information through Intune and Microsoft Entra
    ID.

## Unexpected Result

During the first test, the behavior did not match the expected
assignment scope.

The Intune policy had been assigned to a specific group of test devices,
but LAPS appeared to be affecting a much larger population, including
devices that were not intended to be part of the initial test.

The Intune assignment was reviewed and appeared correct.

This led to an investigation of other endpoint management mechanisms.

## Investigation

The existing endpoint configuration was reviewed.

During the investigation, an existing Group Policy Object was found that
was also configuring Windows LAPS.

The environment therefore had two management paths involved:

``` text
Group Policy
     |
     +---- Windows LAPS

Microsoft Intune
     |
     +---- Windows LAPS
```

This was an important finding because the Intune assignment itself was
not the cause of the unexpected scope.

## Resolution

The existing LAPS GPO was disabled as part of the migration and testing
process.

The test was then repeated.

After the existing GPO configuration was removed, the Intune LAPS policy
behaved as expected and the intended device targeting could be
validated.

## Verification

Verification was performed through both Microsoft Intune and Microsoft
Entra ID.

### Intune Verification

From the device's **Local admin password** view, we verified:

-   The managed local administrator account
-   LAPS password information
-   Password rotation information
-   Next password rotation
-   Availability of the password for authorized retrieval

The actual account name and password are intentionally excluded from
this repository.

### Intune LAPS Verification Screenshot

The screenshot below shows the Windows LAPS information available from the
Microsoft Intune device view.

The screenshot has been sanitized for public documentation. Sensitive
information has been redacted.

![Intune LAPS Local Admin Password - Redacted](../blob/main/docs/images/Intune-LAPS_Local_Admin_Password_Redacted.png?raw=true)

### Microsoft Entra ID Verification

The LAPS information was also checked through Microsoft Entra ID to
confirm that the password backup path was working.

This allowed the complete flow to be validated:

``` text
Intune Policy
     |
     v
Windows LAPS
     |
     v
Local Administrator
     |
     v
Password Backup
     |
     v
Microsoft Entra ID
```

## Pilot Validation

After the configuration conflict was resolved, the implementation was
moved into pilot testing.

Feedback from the selected IT users was used to identify any remaining
operational issues before production rollout.

## Test Result

The key validation criteria were:

  Validation                        Result
  --------------------------------- ------------
  Intune policy assignment          Successful
  Local administrator management    Successful
  Password rotation                 Successful
  Password backup                   Successful
  Microsoft Entra ID verification   Successful
  Targeting after GPO removal       Successful

The exact production values and internal identifiers are not published.
