---
id: OwcKMnALpn7tuBaJY1US
name: Add a new System Info Collector
file_version: 1.0.1
app_version: 0.6.0-0
file_blobs:
  monkey/common/data/system_info_collectors_names.py: 175a054e1408805a4cebbe27e2f9616db40988cf
  monkey/infection_monkey/system_info/collectors/hostname_collector.py: ae9560815d14351f8b5d7c6fd50f6888d9cf4309
  monkey/monkey_island/cc/services/config_schema/definitions/system_info_collector_classes.py: 5f113f4a74200cd306c20f87f99f309a2d77ff6c
  monkey/monkey_island/cc/services/config_schema/monkey.py: b47d6a15bc139dd6102fe805a6a48b13f700a7cd
  monkey/monkey_island/cc/services/telemetry/processing/system_info_collectors/hostname.py: e2de4519cbd71bba70e81cf3ff61817437d95a21
  monkey/monkey_island/cc/services/telemetry/processing/system_info_collectors/system_info_telemetry_dispatcher.py: 639a392ce4321c3ee9dbecae7a8dd372d7116f5a
---

# What are system info collectors?

Well, the name pretty much explains it. They are Monkey classes which collect various information regarding the victim system, such as Environment, SSH Info, Process List, Netstat and more. 

## What should I add? 

A system info collector which collects the hostname of the system.

## Test manually

Once you're done, make sure that your collector:
* Appears in the Island configuration, and is enabled by default
* The collector actually runs when executing a Monkey.
* Results show up in the relevant places:
  * The infection map.
  * The security report.
  * The relevant MITRE techniques.

**There are a lot of hints for this unit - don't be afraid to use them!**

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/common/data/system_info_collectors_names.py
```python
⬜ 1      AWS_COLLECTOR = "AwsCollector"
🟩 2      HOSTNAME_COLLECTOR = "HostnameCollector"
⬜ 3     +# SWIMMER: Collector name goes here.
⬜ 4      ENVIRONMENT_COLLECTOR = "EnvironmentCollector"
⬜ 5      PROCESS_LIST_COLLECTOR = "ProcessListCollector"
⬜ 6      MIMIKATZ_COLLECTOR = "MimikatzCollector"
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/infection_monkey/system_info/collectors/hostname_collector.py
```python
⬜ 1      import logging
🟩 2      import socket
🟩 3      
🟩 4      from common.data.system_info_collectors_names import HOSTNAME_COLLECTOR
🟩 5      from infection_monkey.system_info.system_info_collector import \
🟩 6          SystemInfoCollector
⬜ 7      
⬜ 8      logger = logging.getLogger(__name__)
⬜ 9      
🟩 10     
⬜ 11    +# SWIMMER: The collector class goes here.
🟩 12     class HostnameCollector(SystemInfoCollector):
🟩 13         def __init__(self):
🟩 14             super().__init__(name=HOSTNAME_COLLECTOR)
🟩 15     
🟩 16         def collect(self) -> dict:
🟩 17             return {"hostname": socket.getfqdn()}
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/definitions/system_info_collector_classes.py
```python
⬜ 1      from common.data.system_info_collectors_names import (AWS_COLLECTOR,
⬜ 2                                                            AZURE_CRED_COLLECTOR,
⬜ 3                                                            ENVIRONMENT_COLLECTOR,
🟩 4                                                            HOSTNAME_COLLECTOR,
⬜ 5                                                            MIMIKATZ_COLLECTOR,
⬜ 6                                                            PROCESS_LIST_COLLECTOR)
⬜ 7      
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/definitions/system_info_collector_classes.py
```python
⬜ 37                 "info": "If on AWS, collects more information about the AWS instance currently running on.",
⬜ 38                 "attack_techniques": ["T1082"]
⬜ 39             },
🟩 40             {
⬜ 41    +        # SWIMMER: Collector config goes here. Tip: Hostname collection relates to the T1082 and T1016 techniques.
🟩 42                 "type": "string",
🟩 43                 "enum": [
🟩 44                     HOSTNAME_COLLECTOR
🟩 45                 ],
🟩 46                 "title": "Hostname collector",
🟩 47                 "info": "Collects machine's hostname.",
🟩 48                 "attack_techniques": ["T1082", "T1016"]
🟩 49             },
⬜ 50             {
⬜ 51                 "type": "string",
⬜ 52                 "enum": [
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/monkey.py
```python
⬜ 1      from common.data.system_info_collectors_names import (AWS_COLLECTOR,
⬜ 2                                                            AZURE_CRED_COLLECTOR,
⬜ 3                                                            ENVIRONMENT_COLLECTOR,
🟩 4                                                            HOSTNAME_COLLECTOR,
⬜ 5                                                            MIMIKATZ_COLLECTOR,
⬜ 6                                                            PROCESS_LIST_COLLECTOR)
⬜ 7      
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/monkey.py
```python
⬜ 88                         "default": [
⬜ 89                             ENVIRONMENT_COLLECTOR,
⬜ 90                             AWS_COLLECTOR,
🟩 91                             HOSTNAME_COLLECTOR,
⬜ 92                             PROCESS_LIST_COLLECTOR,
⬜ 93                             MIMIKATZ_COLLECTOR,
⬜ 94                             AZURE_CRED_COLLECTOR
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/telemetry/processing/system_info_collectors/hostname.py
```python
⬜ 1      import logging
⬜ 2      
🟩 3      from monkey_island.cc.models.monkey import Monkey
⬜ 4     +# SWIMMER: This will be useful :) monkey_island.cc.models.monkey.Monkey has the useful
⬜ 5     +# "get_single_monkey_by_guid" and "set_hostname" methods.
⬜ 6      
⬜ 7      logger = logging.getLogger(__name__)
⬜ 8      
⬜ 9      
🟩 10     def process_hostname_telemetry(collector_results, monkey_guid):
⬜ 11    +# SWIMMER: Processing function goes here.
🟩 12         Monkey.get_single_monkey_by_guid(monkey_guid).set_hostname(collector_results["hostname"])
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/telemetry/processing/system_info_collectors/system_info_telemetry_dispatcher.py
```python
⬜ 3      
⬜ 4      from common.data.system_info_collectors_names import (AWS_COLLECTOR,
⬜ 5                                                            ENVIRONMENT_COLLECTOR,
🟩 6                                                            HOSTNAME_COLLECTOR,
⬜ 7                                                            PROCESS_LIST_COLLECTOR)
⬜ 8      from monkey_island.cc.services.telemetry.processing.system_info_collectors.aws import \
⬜ 9          process_aws_telemetry
⬜ 10     from monkey_island.cc.services.telemetry.processing.system_info_collectors.environment import \
⬜ 11         process_environment_telemetry
🟩 12     from monkey_island.cc.services.telemetry.processing.system_info_collectors.hostname import \
🟩 13         process_hostname_telemetry
⬜ 14     from monkey_island.cc.services.telemetry.zero_trust_tests.antivirus_existence import \
⬜ 15         test_antivirus_existence
⬜ 16     
```

<br/>

<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/telemetry/processing/system_info_collectors/system_info_telemetry_dispatcher.py
```python
⬜ 19     SYSTEM_INFO_COLLECTOR_TO_TELEMETRY_PROCESSORS = {
⬜ 20         AWS_COLLECTOR: [process_aws_telemetry],
⬜ 21         ENVIRONMENT_COLLECTOR: [process_environment_telemetry],
🟩 22         HOSTNAME_COLLECTOR: [process_hostname_telemetry],
⬜ 23         PROCESS_LIST_COLLECTOR: [test_antivirus_existence]
⬜ 24     }
⬜ 25     
```

<br/>

System info collectors are useful to get more data for various things, such as ZT tests or MITRE techniques. Take a look at some other techniques!

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://swimm.io/link?l=c3dpbW0lM0ElMkYlMkZyZXBvcyUyRlpnMWZscldSZ3ZsczBjMm1GeURJJTJGZG9jcyUyRk93Y0tNbkFMcG43dHVCYUpZMVVT). Timestamp: 2021-10-14T15:25:55.008Z (UTC)