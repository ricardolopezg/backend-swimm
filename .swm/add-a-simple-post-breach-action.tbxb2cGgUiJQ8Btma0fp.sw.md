---
id: tbxb2cGgUiJQ8Btma0fp
name: Add a simple Post Breach action
file_version: 1.0.2
app_version: 0.9.8-4
file_blobs:
  monkey/common/data/post_breach_consts.py: 25e6679cb1623aae1a732deb05cc011a452743e3
  monkey/infection_monkey/post_breach/actions/add_user.py: 58be89a1f114459aa3548fdeef16226ffe63aea0
  monkey/monkey_island/cc/services/attack/technique_reports/T1136.py: 086a1c1399499897d4e49118332aead8d7d1f3b4
  monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py: f1fe0f6f26030f3e9893bf63df97dd59d3837672
---

Read [our documentation about adding a new PBA](https://www.guardicore.com/infectionmonkey/docs/development/adding-post-breach-actions/).

After that we want you to add the BackdoorUser PBA. The commands that add users for Win and Linux can be retrieved from `get_commands_to_add_user` - make sure you see how to use this function correctly.

Note that the PBA should impact the T1136 MITRE technique as well!

# Manual test to confirm

1.  Run the Monkey Island
    
2.  Make sure your new PBA is enabled by default in the config - for this test, disable network scanning, exploiting, and all other PBAs
    
3.  Run Monkey
    
4.  See the PBA in the security report 5, See the PBA in the MITRE report in the relevant technique

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/common/data/post_breach_consts.py
```python
⬜ 1      POST_BREACH_COMMUNICATE_AS_NEW_USER = "Communicate as new user"
🟩 2      POST_BREACH_BACKDOOR_USER = "Backdoor user"
⬜ 3      POST_BREACH_FILE_EXECUTION = "File execution"
⬜ 4      POST_BREACH_SHELL_STARTUP_FILE_MODIFICATION = "Modify shell startup file"
⬜ 5      POST_BREACH_HIDDEN_FILES = "Hide files and directories"
⬜ 6      POST_BREACH_TRAP_COMMAND = "Execute command when a particular signal is received"
```

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/infection_monkey/post_breach/actions/add_user.py
```python
🟩 1      from common.data.post_breach_consts import POST_BREACH_BACKDOOR_USER
🟩 2      from infection_monkey.config import WormConfiguration
🟩 3      from infection_monkey.post_breach.pba import PBA
🟩 4      from infection_monkey.utils.users import get_commands_to_add_user
🟩 5      
🟩 6      
🟩 7      class BackdoorUser(PBA):
🟩 8          def __init__(self):
🟩 9              linux_cmds, windows_cmds = get_commands_to_add_user(
🟩 10                 WormConfiguration.user_to_add,
🟩 11                 WormConfiguration.remote_user_pass)
🟩 12             super(BackdoorUser, self).__init__(
🟩 13                 POST_BREACH_BACKDOOR_USER,
🟩 14                 linux_cmd=' '.join(linux_cmds),
🟩 15                 windows_cmd=windows_cmds)
🟩 16     
```

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/attack/technique_reports/T1136.py
```python
⬜ 1      from common.data.post_breach_consts import (
🟩 2          POST_BREACH_BACKDOOR_USER, POST_BREACH_COMMUNICATE_AS_NEW_USER)
⬜ 3      from monkey_island.cc.services.attack.technique_reports.pba_technique import \
⬜ 4          PostBreachTechnique
⬜ 5      
⬜ 6      __author__ = "shreyamalviya"
```

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/attack/technique_reports/T1136.py
```python
⬜ 11         unscanned_msg = "Monkey didn't try creating a new user on the network's systems."
⬜ 12         scanned_msg = "Monkey tried creating a new user on the network's systems, but failed."
⬜ 13         used_msg = "Monkey created a new user on the network's systems."
🟩 14         pba_names = [POST_BREACH_BACKDOOR_USER, POST_BREACH_COMMUNICATE_AS_NEW_USER]
⬜ 15     
```

<br/>



<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py
```python
⬜ 4                         "might do after breaching a new machine. Used in ATT&CK and Zero trust reports.",
⬜ 5          "type": "string",
⬜ 6          "anyOf": [
🟩 7              {
🟩 8                  "type": "string",
🟩 9                  "enum": [
🟩 10                     "BackdoorUser"
🟩 11                 ],
🟩 12                 "title": "Back door user",
🟩 13                 "info": "Attempts to create a new user on the system and delete it afterwards.",
🟩 14                 "attack_techniques": ["T1136"]
🟩 15             },
🟩 16             {
⬜ 17                 "type": "string",
⬜ 18                 "enum": [
⬜ 19                     "CommunicateAsNewUser"
```

<br/>

Take a look at the configuration of the island again - see the "command to run after breach" option we offer the user? It's implemented exactly like you did right now but each user can do it for themselves.

However, what if the PBA needs to do stuff which is more complex than just running a few commands? In that case...

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Z2l0aHViJTNBJTNBYmFja2VuZC1zd2ltbSUzQSUzQXJpY2FyZG9sb3Blemc=/docs/tbxb2cGgUiJQ8Btma0fp).