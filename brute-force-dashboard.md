# Brute Force Detection Dashboard – Splunk

This dashboard is designed to help analysts visualize brute force activity across systems.

---

## 📌 Panels

### 1. Top Source IPs with Failed Logins
```spl
sourcetype=apache_access status=401 
| stats count by src_ip 
| sort - count
sourcetype=apache_access status=401 
| timechart span=5m count
sourcetype=apache_access status=401 OR status=200 uri="/login"
| transaction src_ip startswith="status=401" endswith="status=200"
| table src_ip, duration, eventcount

---

## ✅ Step 3: `traffic-spike-dashboard.md`

Paste this:

```markdown
# Login Activity Spike Detection – Splunk Dashboard

This dashboard tracks overall login activity and detects abnormal spikes from single IPs or users.

---

## 📌 Panels

### 1. Total Login Attempts per 5 Minutes
```spl
sourcetype=apache_access uri="/login"
| timechart span=5m count
sourcetype=apache_access uri="/login"
| stats count by src_ip 
| sort - count 
| head 5
sourcetype=apache_access uri="/login"
| bin _time span=5m
| stats count as attempts by _time, src_ip
| eventstats avg(attempts) as avg, stdev(attempts) as stddev
| eval is_anomaly=if(attempts > avg + (2 * stddev), "Yes", "No")
| where is_anomaly="Yes"

---
