# Exchange Contacts SOA Conversion Tool

GUI tool to manage Contact Source of Authority (SOA) conversion between cloud-managed and on-premises-managed by toggling the contact property `isCloudManaged` via the Microsoft Graph onPremisesSyncBehavior API.

## Features

- Automatic permission consent flow for required Graph API permissions
- Display all organizational contacts with their current SOA status (Cloud Managed: True/False)
- Optional filter to hide already converted (cloud-managed) contacts
- Pagination support for large contact lists

## Prerequisites

- PowerShell 5.1 or later
- Microsoft.Graph.Identity.DirectoryManagement PowerShell module (auto-installed if missing)
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
