# Architecture

## Data flow

1. A simulated alert is associated with a Microsoft Sentinel incident.
2. An automation rule configured for **When incident is created** invokes a Consumption Logic App playbook.
3. The **Microsoft Sentinel incident** trigger supplies incident context.
4. **Entities – Get IPs** extracts IP entities from the incident payload.
5. An automatically generated **For each** iterates over those addresses.
6. The built-in HTTP action calls `GET https://api.abuseipdb.com/api/v2/check` with `ipAddress` and `maxAgeInDays=90`.
7. **Add comment to incident (V3)** posts IP reputation context back to the triggering incident.

## Lab resources

| Resource | Name | Role |
| --- | --- | --- |
| Azure resource group | `rg-sentinel-playbooks-lab` | Management and permission boundary |
| Log Analytics workspace | `law-sentinel-playbooks-lab` | Microsoft Sentinel workspace |
| Logic App | `pb-ip-reputation-enrichment` | Incident-triggered SOAR workflow |
| Automation rule | `Auto-Enrich-Incident-IPs-AbuseIPDB` | Launch playbook when a new incident is created |

All were created for a controlled lab. The automation rule was disabled after tests.

## Scope limitation

A second simulated alert was **correlated with an existing incident**, not a newly created incident. Because the automation rule listens for incident creation, no second automatic run occurred. The updated comment formatting was therefore validated by **manually running** the playbook against the existing incident.
