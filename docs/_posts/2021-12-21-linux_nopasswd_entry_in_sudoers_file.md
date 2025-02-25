---
title: "Linux NOPASSWD Entry In Sudoers File"
excerpt: "Sudo and Sudo Caching, Abuse Elevation Control Mechanism"
categories:
  - Endpoint
last_modified_at: 2021-12-21
toc: true
toc_label: ""
tags:
  - Sudo and Sudo Caching
  - Privilege Escalation
  - Defense Evasion
  - Abuse Elevation Control Mechanism
  - Privilege Escalation
  - Defense Evasion
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This analytic is to look for suspicious command lines that may add entry to /etc/sudoers with NOPASSWD attribute in linux platform. This technique is commonly abuse by adversaries, malware author and red teamers to gain elevated privilege to the targeted or compromised host. /etc/sudoers file controls who can run what commands users can execute on the machines and can also control whether user need a password to execute particular commands. This file is composed of aliases (basically variables) and user specifications.

- **Type**: [Anomaly](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2021-12-21
- **Author**: Teoderick Contreras, Splunk
- **ID**: ab1e0d52-624a-11ec-8e0b-acde48001122


#### [ATT&CK](https://attack.mitre.org/)

| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1548.003](https://attack.mitre.org/techniques/T1548/003/) | Sudo and Sudo Caching | Privilege Escalation, Defense Evasion |

| [T1548](https://attack.mitre.org/techniques/T1548/) | Abuse Elevation Control Mechanism | Privilege Escalation, Defense Evasion |

#### Search

```

| tstats `security_content_summariesonly` count min(_time) as firstTime max(_time) as lastTime from datamodel=Endpoint.Processes where Processes.process = "*NOPASSWD:*" by Processes.dest Processes.user Processes.parent_process_name Processes.process_name Processes.process Processes.process_id Processes.parent_process_id Processes.process_guid 
| `drop_dm_object_name(Processes)` 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `linux_nopasswd_entry_in_sudoers_file_filter`
```

#### Macros
The SPL above uses the following Macros:
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)
* [security_content_summariesonly](https://github.com/splunk/security_content/blob/develop/macros/security_content_summariesonly.yml)

Note that `linux_nopasswd_entry_in_sudoers_file_filter` is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* _time
* Processes.dest
* Processes.user
* Processes.parent_process_name
* Processes.process_name
* Processes.process
* Processes.process_id
* Processes.parent_process_id


#### How To Implement
To successfully implement this search, you need to be ingesting logs with the process name, parent process, and command-line executions from your endpoints. If you are using Sysmon, you can use the Add-on for Linux Sysmon from Splunkbase.

#### Known False Positives
Administrator or network operator can execute this command. Please update the filter macros to remove false positives.

#### Associated Analytic story
* [Linux Privilege Escalation](/stories/linux_privilege_escalation)
* [Linux Persistence Techniques](/stories/linux_persistence_techniques)


#### Kill Chain Phase
* Privilege Escalation



#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 64.0 | 80 | 80 | a commandline $process$ executed on $dest$ |


Note that risk score is calculated base on the following formula: `(Impact * Confidence)/100`



#### Reference

* [https://askubuntu.com/questions/334318/sudoers-file-enable-nopasswd-for-user-all-commands](https://askubuntu.com/questions/334318/sudoers-file-enable-nopasswd-for-user-all-commands)
* [https://help.ubuntu.com/community/Sudoers](https://help.ubuntu.com/community/Sudoers)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1548.003/nopasswd_sudoers/sysmon_linux.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1548.003/nopasswd_sudoers/sysmon_linux.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/linux_nopasswd_entry_in_sudoers_file.yml) \| *version*: **1**