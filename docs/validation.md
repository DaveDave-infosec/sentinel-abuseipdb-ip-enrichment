# Validation evidence and troubleshooting

## Test 1: automated enrichment

A clearly labelled simulated alert (**LAB TEST - Suspicious IP Activity**) was submitted with a fictional device `SOC-LAB-TEST-PC01` and `8.8.8.8` as related IP evidence.

**Observed:** Sentinel Incident 1 was created, the incident-created automation executed the playbook, Logic Apps run history showed **Succeeded** (2.72 seconds), and Incident 1 activity contained the AbuseIPDB JSON response posted by `Playbook-pb-ip-reputation-enrichment`.

## Test 2: updated comment formatting

The playbook comment was revised from raw JSON to named fields: IP, abuse confidence score, total reports, ISP, country.

A second simulated alert was linked to **existing Incident 1**, rather than creating a new incident. Consequently, the **When incident is created** automation rule did not produce a second run.

**Resolution:** Used the incident's **Run playbook** menu to execute `pb-ip-reputation-enrichment` manually on the existing incident.

**Observed:** The second run showed **Succeeded** on October 20, 2026. The activity log showed a new `ABUSEIPDB IP REPUTATION REPORT` posted by the playbook. Values displayed: `8.8.8.8`, abuse confidence `0`, reports `234`, ISP `Google LLC`, country `US`.

## Lessons learned

- **Alert creation is not always incident creation:** Incident correlation can attach alerts to an existing incident.
- **Trigger accepted ≠ playbook succeeded:** Confirm Logic Apps **Run history** and incident comments.
- **Formatting changes need validation:** Updating a Logic App definition does not retroactively rewrite earlier comments.
- **Avoid loose incident-update triggers:** They can generate repeat executions when the playbook updates its own incident.

## Evidence checklist

See [screenshots/README.md](../screenshots/README.md). The images are uploaded separately after documentation.
