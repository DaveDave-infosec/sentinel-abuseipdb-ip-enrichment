# Logic App playbook: workflow and expressions

## Workflow

```text
Microsoft Sentinel incident (trigger)
  -> Entities – Get IPs
  -> For each (IPs Address)
       -> HTTP (AbuseIPDB check)
       -> Add comment to incident (V3)
```

The `For each` control ensures that a separate reputation request and comment can be made for each IP entity.

## HTTP request

- **Method:** GET
- **URI:** `https://api.abuseipdb.com/api/v2/check`
- **Headers:** `Accept: application/json` and `Key: <SECRET>`
- **Query:** `ipAddress` set to the loop's dynamic **IPs Address**; `maxAgeInDays=90`
- **Security:** Secure Inputs and Secure Outputs enabled on the HTTP action

Do **not** paste a live API key into code or this repository. The key used in the portal was a private credential. Secure Inputs/Outputs protect run-history display but do not make a hardcoded secret in the Logic App definition safe to export. Prefer Azure Key Vault for production workflows.

## Incident comment

The V3 comment action receives the dynamic **Incident ARM ID** from the trigger. The final message uses the current IP token and expression outputs from the HTTP response:

```text
ABUSEIPDB IP REPUTATION REPORT

IP Address: [IPs Address dynamic token]
Abuse Confidence Score: [expression below]
Total Reports: [expression below]
ISP: [expression below]
Country: [expression below]
```

The expressions used:

```text
body('HTTP')?['data']?['abuseConfidenceScore']
body('HTTP')?['data']?['totalReports']
body('HTTP')?['data']?['isp']
body('HTTP')?['data']?['countryCode']
```

These expressions are Logic Apps expressions, not KQL. Their action reference (`HTTP`) must match the name of the HTTP action in the designer.

## Automation behavior

- Trigger: **When incident is created**
- Action: **Run playbook** → `pb-ip-reputation-enrichment`
- After testing: automation rule **Disabled**
- Incident update events were not enabled as an automatic trigger, avoiding the risk of repeated execution when the workflow writes comments.

## Interpretation

Reputation responses are **triage context**, not a verdict. For the test IP `8.8.8.8`, the final check showed 0% abuse confidence despite historical reports; analysts should corroborate with additional telemetry.
