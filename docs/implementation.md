# Windows LAPS Implementation

## Overview

This document describes the implementation approach used to move local
administrator password management from a manually maintained process to
Windows LAPS managed through Microsoft Intune and Microsoft Entra ID.

The objective was to give the IT team a controlled way to manage local
administrator accounts and retrieve passwords when required, while
removing the operational dependency on a shared spreadsheet.

## Previous State

Before Windows LAPS was introduced, local administrator passwords were
maintained manually in an Excel spreadsheet.

The process created two main concerns:

-   Passwords had to be maintained manually.
-   Keeping the password record accurate as devices changed or passwords
    changed was difficult.

The implementation goal was to move password generation, management,
rotation, and backup into the Windows platform.

## Target Architecture

The target management flow was:

``` text
Microsoft Intune
        |
        | Windows LAPS Policy
        v
Windows 11 Device
        |
        | Windows LAPS
        v
Managed Local Administrator Account
        |
        | Password Backup
        v
Microsoft Entra ID
```

## Deployment Model

The deployment was deliberately staged:

``` text
Selected IT Devices
        |
        v
Pilot Groups
        |
        v
Production
```

The initial test population was intentionally small so that
configuration problems could be identified before wider deployment.

## Policy Capabilities

The implementation used Windows LAPS capabilities including:

-   Password backup to Microsoft Entra ID
-   Automatic password rotation
-   Password complexity requirements
-   Post-authentication password reset actions
-   Automatic local administrator account management
-   Account-name randomization where supported
-   Management of the local administrator account

Exact production values and internal naming conventions are
intentionally excluded from the public repository.

## Account Name Randomization

The environment uses Windows LAPS automatic account management and
account-name randomization on supported Windows versions.

For public documentation, the actual naming convention is redacted.

Example:

``` text
<REDACTED_PREFIX>_XXXXXX
```

The example is illustrative only and does not represent the
organization's actual naming convention.

## Security Considerations

The public project intentionally excludes:

-   Passwords
-   Password fragments
-   Device names
-   Tenant identifiers
-   Security identifiers
-   Internal policy names
-   Internal assignment group names
-   Internal URLs
-   Client secrets
-   Access tokens
-   Company-specific security configuration

Screenshots are sanitized before publication.

## Outcome

The implementation replaced a manually maintained password-recording
process with Windows-managed local administrator credentials and
centralized password backup through Microsoft Entra ID.

The staged rollout also provided a controlled way to identify
configuration conflicts before broader deployment.
