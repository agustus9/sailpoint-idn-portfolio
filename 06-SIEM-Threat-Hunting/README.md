# 🔍 SIEM Threat Hunting & Identity Security Monitoring

## 📋 Scenario & Business Problem
Identity ecosystems are a primary target for advanced persistent threats (APTs). A common attack vector involves compromising an administrative identity to quietly modify critical platform roles, or executing an "MFA fatigue" push-notification spam attack to bypass standard access gates. 

Without specialized security monitoring, these identity security incidents blend into thousands of normal audit records. Security Operations Centers (SOC) require optimized hunting queries to quickly isolate suspicious behavior—such as a sudden wave of denied multi-factor prompts or out-of-band administrative capability modifications—before an account takeover is finalized.

## 💡 Solution Architecture
This project establishes high-fidelity, intermediate-level detection rules written in **Kusto Query Language (KQL)**, **Splunk Search Processing Language (SPL)**, and **Ariel Query Language (AQL)**.
1. **Privilege Modification Monitoring:** Audits centralized log feeds for administrative changes like new capability assignments or added account roles.
2. **MFA Fatigue Alerting:** Tracks rapid sequences of denied or failed push notifications on a per-user basis to catch user capitulation.
3. **High-Risk Sign-In Tracking:** Flags account authentication anomalies coming from unexpected geographic locations or flagged risk levels.

## 🏗️ Technical Components
* **Component Type:** SIEM Detection Rules & Threat Hunting Logic
* **Query Standards:** Microsoft Sentinel KQL, Splunk SPL, IBM QRadar AQL
* **Log Sources Evaluated:** Application Syslog Trails, MicrosoftEntraID SignInLogs, Okta Audit Syslog

---

## 🛠️ Intermediate Threat Hunting Queries

### Query 1: Microsoft Sentinel (KQL) - High-Risk Administrative Changes
This query scans the system logs to identify instances where critical access changes, role additions, or capability updates are pushed, tracking which administrator initiated the action.

```kusto
Syslog


| where ProcessName == "identity.governance.engine" or Message contains "sailpoint"
| where Message contains "CapabilityAssignment" or Message contains "addRole"
| summarize EventCount = count() by Computer, HostIP, ProcessName, Message

| sort by EventCount desc
```

### Query 2: Splunk (SPL) - Okta MFA Fatigue / Push Spam Detection
This query isolates scenarios where a single user identity rejects multiple continuous multi-factor authentication (MFA) prompts within the log window, indicating an active push-notification spamming attempt.

```splunk
index=security sourcetype="okta:system:log" displayMessage="*MFA*REJECT*" OR displayMessage="*Authentication failed*"
| stats count as FailureCount by actor.alternateId, client.ipAddress, displayMessage
| where FailureCount >= 3
| sort - FailureCount
```

### Query 3: IBM QRadar (AQL) - Unauthorized Group & Privilege Modifications
This query monitors QRadar event logs for target environment modifications that signal lateral group changes or privilege tampering outside standard maintenance windows.

```aql
SELECT username AS 'Admin User', IdentityName AS 'Target Identity', EventName(eventid) AS 'Action', DATEFORMAT(starttime, 'yyyy-MM-dd HH:mm:ss') AS 'Event Time'
FROM events
WHERE (payload CONTAINS 'addMemberToRole' OR payload CONTAINS 'CapabilityAssignment' OR payload CONTAINS 'addRole')
ORDER BY starttime DESC
```

---

## 🚀 How to Import & Run
1. **For Azure/Sentinel:** Navigate to **Microsoft Sentinel** -> **Logs**, paste the KQL query, and select your desired time range to hunt for outliers.
2. **For Splunk:** Paste the SPL query directly into the Search bar inside the **Search & Reporting** app window.
3. **For QRadar:** Go to the **Log Activity** tab, select **Advanced Search** from the options bar, paste the AQL query, and hit search.
