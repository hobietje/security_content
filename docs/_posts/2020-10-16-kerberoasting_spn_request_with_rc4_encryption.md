---
title: "Kerberoasting spn request with RC4 encryption"
excerpt: "Kerberoasting, Steal or Forge Kerberos Tickets"
categories:
  - Endpoint
last_modified_at: 2020-10-16
toc: true
toc_label: ""
tags:
  - Kerberoasting
  - Credential Access
  - Steal or Forge Kerberos Tickets
  - Credential Access
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search detects a potential kerberoasting attack via service principal name requests

- **Type**: [TTP](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: 
- **Last Updated**: 2020-10-16
- **Author**: Jose Hernandez, Patrick Bareiss, Splunk
- **ID**: 5cc67381-44fa-4111-8a37-7a230943f027


#### [ATT&CK](https://attack.mitre.org/)

| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1558.003](https://attack.mitre.org/techniques/T1558/003/) | Kerberoasting | Credential Access |

| [T1558](https://attack.mitre.org/techniques/T1558/) | Steal or Forge Kerberos Tickets | Credential Access |

#### Search

```
`wineventlog_security` EventCode=4769 Ticket_Options=0x40810000 Ticket_Encryption_Type=0x17 
| stats count min(_time) as firstTime max(_time) as lastTime by dest, service, service_id, Ticket_Encryption_Type, Ticket_Options 
| `security_content_ctime(lastTime)` 
| `security_content_ctime(firstTime)` 
| `kerberoasting_spn_request_with_rc4_encryption_filter`
```

#### Macros
The SPL above uses the following Macros:
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)
* [wineventlog_security](https://github.com/splunk/security_content/blob/develop/macros/wineventlog_security.yml)

Note that `kerberoasting_spn_request_with_rc4_encryption_filter` is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* _time
* EventCode
* Ticket_Options
* Ticket_Encryption_Type
* dest
* service
* service_id


#### How To Implement
You must be ingesting endpoint data that tracks process activity, and include the windows security event logs that contain kerberos

#### Known False Positives
Older systems that support kerberos RC4 by default NetApp may generate false positives

#### Associated Analytic story
* [Windows Privilege Escalation](/stories/windows_privilege_escalation)


#### Kill Chain Phase
* Privilege Escalation



#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 72.0 | 90 | 80 | Potential kerberoasting attack via service principal name requests detected on $dest$ |


Note that risk score is calculated base on the following formula: `(Impact * Confidence)/100`



#### Reference

* [https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1208/T1208.md](https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1208/T1208.md)
* [https://www.trimarcsecurity.com/post/trimarcresearch-detecting-kerberoasting-activity](https://www.trimarcsecurity.com/post/trimarcresearch-detecting-kerberoasting-activity)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1558.003/atomic_red_team/windows-security.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1558.003/atomic_red_team/windows-security.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/kerberoasting_spn_request_with_rc4_encryption.yml) \| *version*: **3**