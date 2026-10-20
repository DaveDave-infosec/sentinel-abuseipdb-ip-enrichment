# Security, cost and cleanup notes

## Credential safety

- The AbuseIPDB API key is a credential; **never** commit, screenshot, or publish it.
- Secure Inputs / Secure Outputs were enabled for the HTTP action. These settings help hide run details but **do not remove the credential from the Logic App definition**.
- Production improvement: put the key in **Azure Key Vault**, use a managed identity, and restrict secret access.
- If a secret is ever disclosed, revoke/rotate it in AbuseIPDB.

## Access control

- The Logic App's system-assigned managed identity was enabled.
- The managed identity received **Microsoft Sentinel Contributor** at `rg-sentinel-playbooks-lab` scope to allow incident updates.
- Microsoft Sentinel was separately authorized to run playbooks in that resource group.
- Production systems should evaluate narrower custom permissions.

## Lab boundaries

- A benign public DNS IP, `8.8.8.8`, was used for test data; no finding of maliciousness is implied.
- A fictional impacted device was used.
- No Azure Linux honeypot, Internet-exposed Windows RDP host, or geolocation workbook was deployed in this project.

## After testing

- Automation rule `Auto-Enrich-Incident-IPs-AbuseIPDB` was **disabled**.
- Logic App remained **enabled** at the time of documented screenshots.
- **Important:** Disabled automation does not necessarily stop Logic App, Sentinel, or Log Analytics billing. Azure free-credit/trial status and resources must be reviewed separately.
- When evidence has been saved and the lab is no longer needed, delete the project resource group and confirm resource removal; cancel the Azure subscription if no other resources are needed. Deletion is irreversible, so keep your screenshots and documentation first.
