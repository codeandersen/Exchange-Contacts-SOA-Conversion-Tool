# Exchange Contacts SOA Conversion Tool

GUI tool to manage Contact Source of Authority (SOA) conversion between cloud-managed and on-premises-managed by toggling the contact property `isCloudManaged` via the Microsoft Graph onPremisesSyncBehavior API.

## Features

- Automatic permission consent flow for required Graph API permissions
- Display all organizational contacts with their current SOA status (Cloud Managed: True/False)
- Optional filter to hide already converted (cloud-managed) contacts
- Pagination support for large contact lists

## Prerequisites

- PowerShell 5.1 or later
- Microsoft.Graph.Authentication PowerShell module (auto-installed if missing; installed with `AllUsers` scope when run elevated, otherwise `CurrentUser`)
- Consent to the `Contacts-OnPremisesSyncBehavior.ReadWrite.All` permission in Microsoft Graph (tool prompts for consent on first connect)

## Usage

```powershell
# Run from the script directory
.\Exchange-Contacts-SOA-Conversion-Tool.ps1

# Optionally specify a tenant ID (useful for multi-tenant/partner scenarios)
.\Exchange-Contacts-SOA-Conversion-Tool.ps1 -TenantId "00000000-0000-0000-0000-000000000000"
```

## How It Works

1. Click **Connect to Graph** — the tool connects to Microsoft Graph and triggers a consent prompt for the required permissions if not already granted.
2. Contacts are listed with columns: Display Name, Email Address, Company, Synced from On-Prem, Cloud Managed.
3. Select one or more contacts and click **Convert to Cloud Managed** to set `isCloudManaged = true`.
4. Click **Roll Back to On-Prem** to set `isCloudManaged = false` (change completes after next Connect Sync cycle).
5. Use **Hide Converted Contacts** to filter the list.
6. Logs are written to `ContactSOAConversion_yyyyMMdd_HHmm.log` next to the script.

## Troubleshooting

**Install-Module reports success, but the tool still says the module cannot be found/imported**

This usually means the folder where the module was installed is not in `$env:PSModulePath` for the session — common on Exchange or managed servers where a user-level `PSModulePath` is set or Documents is redirected. The tool detects this and adds the folder to `PSModulePath` for the current session (logged as a WARNING). If it still fails, run these diagnostics in the same PowerShell window:

```powershell
$env:PSModulePath -split ';'
[Environment]::GetFolderPath('MyDocuments')
Get-InstalledModule Microsoft.Graph.Authentication | Select-Object Version, InstalledLocation
```

The `Modules` folder that is the grandparent of `InstalledLocation` must appear in `$env:PSModulePath`. To fix permanently, install for all users from an elevated PowerShell window (installs to `C:\Program Files\WindowsPowerShell\Modules`, which is always on the path):

```powershell
Install-Module -Name Microsoft.Graph.Authentication -Scope AllUsers
```

## Documentation

See the official Microsoft documentation for Contact SOA:

https://learn.microsoft.com/en-us/entra/identity/hybrid/how-to-user-source-of-authority-configure#configure-contact-soa

## Notes

- The required permission is `Contacts-OnPremisesSyncBehavior.ReadWrite.All` (not the group permission).
- Organizational contacts are retrieved via `GET /v1.0/contacts`.
- SOA status is read/written via `GET/PATCH /v1.0/contacts/{id}/onPremisesSyncBehavior`.

## Author

BLOG: http://www.hcandersen.net
Twitter: @dk_hcandersen
LinkedIn: https://www.linkedin.com/in/hanschrandersen/

## License

MIT License — feel free to distribute and use as you like.

## Disclaimer

This script is provided AS-IS, with no warranty — use at your own risk.
