---
id: JFXftJml8DpmuCPBA9rL
name: Add details about your new PBA
file_version: 1.0.2
app_version: 0.9.8-4
file_blobs:
  monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py: f1fe0f6f26030f3e9893bf63df97dd59d3837672
---

In order to make sure that the new `ScheduleJobs` PBA is shown in the configuration on the Monkey Island, you need to add its details to the configuration file(s). <br><br>

Since this particular PBA is related to the MITRE techniques [T1168](https://attack.mitre.org/techniques/T1168) and [T1053](https://attack.mitre.org/techniques/T1053), make sure to link the PBA with these techniques in the configuration as well. <br><br>

Each part of the configuration has an important role

*   _enum_ — contains the relevant PBA's class name(s)
    
*   _title_ — holds the name of the PBA which is displayed in the configuration on the Monkey Island
    
*   _info_ — consists of an elaboration on the PBA's working which is displayed in the configuration on the Monkey Island
    
*   _attack\_techniques_ — has the IDs of the MITRE techniques associated with the PBA
    

## Manual test

Once you think you're done...

*   Run the Monkey Island
    
*   You should be able to see your new PBA under the "Monkey" tab in the configuration, along with its information when you click on it
    
*   Further, when you enable/disable the associated MITRE techniques under the ATT&CK tab in the configuration, the PBA should also be enabled/disabled

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py
```python
⬜ 62                         "Removes the file afterwards.",
⬜ 63                 "attack_techniques": ["T1166"]
⬜ 64             },
🟩 65             {
🟩 66                 "type": "string",
🟩 67                 "enum": [
🟩 68                     "ScheduleJobs"
🟩 69                 ],
🟩 70                 "title": "Job scheduling",
🟩 71                 "info": "Attempts to create a scheduled job on the system and remove it.",
🟩 72                 "attack_techniques": ["T1168", "T1053"]
🟩 73             },
🟩 74             {
⬜ 75                 "type": "string",
⬜ 76                 "enum": [
⬜ 77                     "Timestomping"
```

<br/>

*   The PBA details in this file are reflected on the Monkey Island in the PBA configuration.
    
*   PBAs are also linked to the relevant MITRE techniques in this file, whose results can then be seen in the MITRE ATT&CK report on the Monkey Island.

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Z2l0aHViJTNBJTNBYmFja2VuZC1zd2ltbSUzQSUzQXJpY2FyZG9sb3Blemc=/docs/JFXftJml8DpmuCPBA9rL).