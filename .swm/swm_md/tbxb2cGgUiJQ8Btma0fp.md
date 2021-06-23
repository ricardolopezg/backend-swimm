<div align="center" style="background-color: #e5ecff; color: black"><br/><div>DOC</div><h1>Add a simple Post Breach action</h1><br/></div>
<br/>

### Files Used
📄 monkey/common/data/post_breach_consts.py

📄 monkey/infection_monkey/post_breach/actions/add_user.py

📄 monkey/monkey_island/cc/services/attack/technique_reports/T1136.py

📄 monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py


<br/>

Read [our documentation about adding a new PBA](https://www.guardicore.com/infectionmonkey/docs/development/adding-post-breach-actions/).

After that we want you to add the BackdoorUser PBA. The commands that add users for Win and Linux can be retrieved from `get_commands_to_add_user` - make sure you see how to use this function correctly. 

Note that the PBA should impact the T1136 MITRE technique as well! 

# Manual test to confirm

1. Run the Monkey Island
2. Make sure your new PBA is enabled by default in the config - for this test, disable network scanning, exploiting, and all other PBAs
3. Run Monkey
4. See the PBA in the security report
5, See the PBA in the MITRE report in the relevant technique


<br/>

<div style="background: #e5ecff; padding: 10px 10px 10px 10px; border-bottom: 1px solid #c1c7d0; border-radius: 4px; color: black">    📄 monkey/common/data/post_breach_consts.py ✅ Up to Date*

   </div>

```python
⬜ 1      POST_BREACH_COMMUNICATE_AS_NEW_USER = "Communicate as new user"
🟩 2      POST_BREACH_BACKDOOR_USER = "Backdoor user"
⬜ 3     +# Swimmer: PUT THE NEW CONST HERE!
⬜ 4      POST_BREACH_FILE_EXECUTION = "File execution"
⬜ 5      POST_BREACH_SHELL_STARTUP_FILE_MODIFICATION = "Modify shell startup file"
⬜ 6      POST_BREACH_HIDDEN_FILES = "Hide files and directories"
```
<br/>

<div style="background: #e5ecff; padding: 10px 10px 10px 10px; border-bottom: 1px solid #c1c7d0; border-radius: 4px; color: black">    📄 monkey/infection_monkey/post_breach/actions/add_user.py ✅ Up to Date*

   </div>

```python
🟩 1      from common.data.post_breach_consts import POST_BREACH_BACKDOOR_USER
🟩 2      from infection_monkey.config import WormConfiguration
⬜ 3      from infection_monkey.post_breach.pba import PBA
⬜ 4      from infection_monkey.utils.users import get_commands_to_add_user
⬜ 5      
⬜ 6      
⬜ 7      class BackdoorUser(PBA):
⬜ 8          def __init__(self):
🟩 9              linux_cmds, windows_cmds = get_commands_to_add_user(
⬜ 10    +        pass  # Swimmer: Impl here!
🟩 11                 WormConfiguration.user_to_add,
🟩 12                 WormConfiguration.remote_user_pass)
🟩 13             super(BackdoorUser, self).__init__(
🟩 14                 POST_BREACH_BACKDOOR_USER,
🟩 15                 linux_cmd=' '.join(linux_cmds),
🟩 16                 windows_cmd=windows_cmds)
```
<br/>

<div style="background: #e5ecff; padding: 10px 10px 10px 10px; border-bottom: 1px solid #c1c7d0; border-radius: 4px; color: black">    📄 monkey/monkey_island/cc/services/attack/technique_reports/T1136.py ✅ Up to Date*

   </div>

```python
⬜ 1      from common.data.post_breach_consts import (
🟩 2          POST_BREACH_BACKDOOR_USER, POST_BREACH_COMMUNICATE_AS_NEW_USER)
⬜ 3     +    POST_BREACH_COMMUNICATE_AS_NEW_USER)
⬜ 4      from monkey_island.cc.services.attack.technique_reports.pba_technique import \
⬜ 5          PostBreachTechnique
⬜ 6      
```
<br/>

<div style="background: #e5ecff; padding: 10px 10px 10px 10px; border-bottom: 1px solid #c1c7d0; border-radius: 4px; color: black">    📄 monkey/monkey_island/cc/services/attack/technique_reports/T1136.py ✅ Up to Date*

   </div>

```python
⬜ 11         unscanned_msg = "Monkey didn't try creating a new user on the network's systems."
⬜ 12         scanned_msg = "Monkey tried creating a new user on the network's systems, but failed."
⬜ 13         used_msg = "Monkey created a new user on the network's systems."
🟩 14         pba_names = [POST_BREACH_BACKDOOR_USER, POST_BREACH_COMMUNICATE_AS_NEW_USER]
⬜ 15    +    pba_names = [POST_BREACH_COMMUNICATE_AS_NEW_USER]
```
<br/>

<div style="background: #e5ecff; padding: 10px 10px 10px 10px; border-bottom: 1px solid #c1c7d0; border-radius: 4px; color: black">    📄 monkey/monkey_island/cc/services/config_schema/definitions/post_breach_actions.py ✅ Up to Date*

   </div>

```python
⬜ 4                         "might do after breaching a new machine. Used in ATT&CK and Zero trust reports.",
⬜ 5          "type": "string",
⬜ 6          "anyOf": [
🟩 7              {
⬜ 8     +        # Swimmer: Add new PBA here to config!
🟩 9                  "type": "string",
🟩 10                 "enum": [
🟩 11                     "BackdoorUser"
🟩 12                 ],
🟩 13                 "title": "Back door user",
🟩 14                 "info": "Attempts to create a new user on the system and delete it afterwards.",
🟩 15                 "attack_techniques": ["T1136"]
🟩 16             },
⬜ 17             {
⬜ 18                 "type": "string",
⬜ 19                 "enum": [
```
<br/>

Take a look at the configuration of the island again - see the "command to run after breach" option we offer the user? It's implemented exactly like you did right now but each user can do it for themselves. 

However, what if the PBA needs to do stuff which is more complex than just running a few commands? In that case... 

<br/>

<br/><br/>

This file was generated by Swimm. [Click here to view it in the app](https://swimm.io/link?l=c3dpbW0lM0ElMkYlMkZyZXBvcyUyRlpnMWZscldSZ3ZsczBjMm1GeURJJTJGZG9jcyUyRnRieGIyY0dnVWlKUThCdG1hMGZw). Timestamp: 2021-06-23T14:09:35.012Z (UTC)
