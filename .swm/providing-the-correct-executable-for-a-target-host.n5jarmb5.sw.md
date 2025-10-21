---
title: Providing the correct executable for a target host
---
This document describes how the Infection Monkey system selects and retrieves the correct executable file for a target host, based on its operating system and architecture. The flow receives host OS and architecture information as input and returns a valid Infection Monkey executable file for deployment. The agent ensures it always runs the appropriate version by requesting executable details from the control server, validating the response, and either downloading the file or using an existing local copy.

```mermaid
flowchart TD
  node1["Selecting the executable for the target OS"]
  click node1 goToHeading "Selecting the executable for the target OS"
  node2["Requesting executable details from the control server"]
  click node2 goToHeading "Requesting executable details from the control server"
  node3["Checking for existing executable"]
  click node3 goToHeading "Checking for existing executable"
  node1 --> node2 --> node3
```

# Where is this flow used?

This flow is used multiple times in the codebase as represented in the following diagram:

```mermaid
graph TD;
      58aefb35102751c0a108ba38ac84972510e928b5fedff512cb6dd6f2fc1c822d(monkey/…/tools/helpers.py::get_target_monkey_by_os) --> 2310c31d52c3781d8cd0f69be78442d215da9a45cbb22ade248425b25ef5add8(monkey/infection_monkey/control.py::download_monkey_exe_by_os):::mainFlowStyle

bde68f1a7c5b7ee69d44b2626410dac61501d8e7d98d61fe6e121b08d6c98b12(monkey/infection_monkey/windows_upgrader.py::upgrade) --> 2310c31d52c3781d8cd0f69be78442d215da9a45cbb22ade248425b25ef5add8(monkey/infection_monkey/control.py::download_monkey_exe_by_os):::mainFlowStyle

7856161e98f53449b9f29bd22c96a7d95ef6d80047447c8d555866d69d12e904(monkey/infection_monkey/monkey.py::upgrade_to_64_if_needed) --> bde68f1a7c5b7ee69d44b2626410dac61501d8e7d98d61fe6e121b08d6c98b12(monkey/infection_monkey/windows_upgrader.py::upgrade)

ad11e0ed54901ad3f46d4e4b3aab4db524bfa82956010b8a2aa716e725cd8704(monkey/infection_monkey/monkey.py::start) --> 7856161e98f53449b9f29bd22c96a7d95ef6d80047447c8d555866d69d12e904(monkey/infection_monkey/monkey.py::upgrade_to_64_if_needed)


classDef mainFlowStyle color:#000000,fill:#7CB9F4
classDef rootsStyle color:#000000,fill:#00FFF4
classDef Style1 color:#000000,fill:#00FFAA
classDef Style2 color:#000000,fill:#FFFF00
classDef Style3 color:#000000,fill:#AA7CB9

%% Swimm:
%% graph TD;
%%       58aefb35102751c0a108ba38ac84972510e928b5fedff512cb6dd6f2fc1c822d(<SwmPath>[monkey/…/tools/helpers.py](monkey/infection_monkey/exploit/tools/helpers.py)</SwmPath>::get_target_monkey_by_os) --> 2310c31d52c3781d8cd0f69be78442d215da9a45cbb22ade248425b25ef5add8(<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>::<SwmToken path="monkey/infection_monkey/control.py" pos="215:3:3" line-data="    def download_monkey_exe_by_os(is_windows, is_32bit):">`download_monkey_exe_by_os`</SwmToken>):::mainFlowStyle
%% 
%% bde68f1a7c5b7ee69d44b2626410dac61501d8e7d98d61fe6e121b08d6c98b12(<SwmPath>[monkey/infection_monkey/windows_upgrader.py](monkey/infection_monkey/windows_upgrader.py)</SwmPath>::upgrade) --> 2310c31d52c3781d8cd0f69be78442d215da9a45cbb22ade248425b25ef5add8(<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>::<SwmToken path="monkey/infection_monkey/control.py" pos="215:3:3" line-data="    def download_monkey_exe_by_os(is_windows, is_32bit):">`download_monkey_exe_by_os`</SwmToken>):::mainFlowStyle
%% 
%% 7856161e98f53449b9f29bd22c96a7d95ef6d80047447c8d555866d69d12e904(<SwmPath>[monkey/infection_monkey/monkey.py](monkey/infection_monkey/monkey.py)</SwmPath>::upgrade_to_64_if_needed) --> bde68f1a7c5b7ee69d44b2626410dac61501d8e7d98d61fe6e121b08d6c98b12(<SwmPath>[monkey/infection_monkey/windows_upgrader.py](monkey/infection_monkey/windows_upgrader.py)</SwmPath>::upgrade)
%% 
%% ad11e0ed54901ad3f46d4e4b3aab4db524bfa82956010b8a2aa716e725cd8704(<SwmPath>[monkey/infection_monkey/monkey.py](monkey/infection_monkey/monkey.py)</SwmPath>::start) --> 7856161e98f53449b9f29bd22c96a7d95ef6d80047447c8d555866d69d12e904(<SwmPath>[monkey/infection_monkey/monkey.py](monkey/infection_monkey/monkey.py)</SwmPath>::upgrade_to_64_if_needed)
%% 
%% 
%% classDef mainFlowStyle color:#000000,fill:#7CB9F4
%% classDef rootsStyle color:#000000,fill:#00FFF4
%% classDef Style1 color:#000000,fill:#00FFAA
%% classDef Style2 color:#000000,fill:#FFFF00
%% classDef Style3 color:#000000,fill:#AA7CB9
```

# Selecting the executable for the target OS

This section governs how the Infection Monkey system selects the correct executable file for the target operating system and architecture, ensuring that the infection tool runs properly on the intended host. The process involves spoofing host OS information and retrieving the appropriate executable filename and size from the control server.

<SwmSnippet path="/monkey/infection_monkey/control.py" line="215">

---

In <SwmToken path="monkey/infection_monkey/control.py" pos="215:3:3" line-data="    def download_monkey_exe_by_os(is_windows, is_32bit):">`download_monkey_exe_by_os`</SwmToken>, we kick off by spoofing the OS and architecture info and sending it to the control server to get the right executable filename and size. We need to call <SwmToken path="monkey/infection_monkey/control.py" pos="216:10:10" line-data="        filename, size = ControlClient.get_monkey_exe_filename_and_size_by_host_dict(">`get_monkey_exe_filename_and_size_by_host_dict`</SwmToken> next because it fetches the actual file details based on the target environment, not just a static value.

```python
    def download_monkey_exe_by_os(is_windows, is_32bit):
        filename, size = ControlClient.get_monkey_exe_filename_and_size_by_host_dict(
            ControlClient.spoof_host_os_info(is_windows, is_32bit))
        if filename is None:
            return None
```

---

</SwmSnippet>

## Requesting executable details from the control server

This section governs how the Infection Monkey client requests the correct executable details from the control server, based on the host's operating system and architecture. The goal is to ensure that the client receives the appropriate executable file name and its size for deployment on the target host.

<SwmSnippet path="/monkey/infection_monkey/control.py" line="277">

---

In <SwmToken path="monkey/infection_monkey/control.py" pos="277:3:3" line-data="    def get_monkey_exe_filename_and_size_by_host_dict(host_dict):">`get_monkey_exe_filename_and_size_by_host_dict`</SwmToken>, we send a POST request to the control server with the host's OS and architecture info. The next step is handled by the server-side logic in <SwmPath>[monkey/infection_monkey/monkey.py](monkey/infection_monkey/monkey.py)</SwmPath>, which figures out the right executable and returns its details.

```python
    def get_monkey_exe_filename_and_size_by_host_dict(host_dict):
        if not WormConfiguration.current_server:
            return None, None
        try:
            reply = requests.post("https://%s/api/monkey/download" % (WormConfiguration.current_server,),  # noqa: DUO123
                                  data=json.dumps(host_dict),
                                  headers={'content-type': 'application/json'},
                                  verify=False, proxies=ControlClient.proxies)
```

---

</SwmSnippet>

### Sending the executable request to the server

This section governs the process of sending executable requests from the client to the server, ensuring that requests are valid, authorized, and properly formatted before transmission.

| Category        | Rule Name                       | Description                                                                                        |
| --------------- | ------------------------------- | -------------------------------------------------------------------------------------------------- |
| Data validation | User authentication requirement | Only authenticated users are allowed to send executable requests to the server.                    |
| Data validation | Valid action enforcement        | Executable requests must specify a valid action or command recognized by the server.               |
| Data validation | Mandatory parameter inclusion   | Requests must include all mandatory parameters required for the specified action.                  |
| Business logic  | Secure transmission enforcement | Executable requests must be transmitted over secure channels to prevent interception or tampering. |

See <SwmLink doc-title="Registering and Updating Monkey Agents">[Registering and Updating Monkey Agents](/.swm/registering-and-updating-monkey-agents.u9godf89.sw.md)</SwmLink>

### Processing the server's response

<SwmSnippet path="/monkey/infection_monkey/control.py" line="285">

---

Back in <SwmToken path="monkey/infection_monkey/control.py" pos="216:10:10" line-data="        filename, size = ControlClient.get_monkey_exe_filename_and_size_by_host_dict(">`get_monkey_exe_filename_and_size_by_host_dict`</SwmToken>, we just got the response from the control server. If the response is good and has a filename, we grab the filename and size and return them. If not, we bail out with None values.

```python
            if 200 == reply.status_code:
                result_json = reply.json()
                filename = result_json.get('filename')
                if not filename:
                    return None, None
                size = result_json.get('size')
                return filename, size
            else:
                return None, None

        except Exception as exc:
            LOG.warning("Error connecting to control server %s: %s",
                        WormConfiguration.current_server, exc)

        return None, None
```

---

</SwmSnippet>

## Fetching the executable file

<SwmSnippet path="/monkey/infection_monkey/control.py" line="220">

---

In <SwmToken path="monkey/infection_monkey/control.py" pos="215:3:3" line-data="    def download_monkey_exe_by_os(is_windows, is_32bit):">`download_monkey_exe_by_os`</SwmToken>, after getting the filename and size, we move on to <SwmToken path="monkey/infection_monkey/control.py" pos="220:5:5" line-data="        return ControlClient.download_monkey_exe_by_filename(filename, size)">`download_monkey_exe_by_filename`</SwmToken> to actually fetch the file or check if it's already available.

```python
        return ControlClient.download_monkey_exe_by_filename(filename, size)
```

---

</SwmSnippet>

# Checking for existing executable

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is control server configured?"}
    click node1 openCode "monkey/infection_monkey/control.py:248:249"
    node1 -->|"No"| node2["Return nothing"]
    click node2 openCode "monkey/infection_monkey/control.py:249:249"
    node1 -->|"Yes"| node3{"Does local file 'filename' exist and match 'size'?"}
    click node3 openCode "monkey/infection_monkey/control.py:251:253"
    node3 -->|"Yes"| node4["Return local file"]
    click node4 openCode "monkey/infection_monkey/control.py:253:253"
    node3 -->|"No"| node5["Download 'filename' from current_server"]
    click node5 openCode "monkey/infection_monkey/control.py:255:258"
    node5 --> node6
    subgraph loop1["For each chunk in the download"]
      node6["Write chunk to local file"]
      click node6 openCode "monkey/infection_monkey/control.py:261:263"
    end
    loop1 --> node7{"Does downloaded file match 'size'?"}
    click node7 openCode "monkey/infection_monkey/control.py:265:265"
    node7 -->|"Yes"| node8["Return local file"]
    click node8 openCode "monkey/infection_monkey/control.py:266:266"
    node7 -->|"No"| node2
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is control server configured?"}
%%     click node1 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:248:249"
%%     node1 -->|"No"| node2["Return nothing"]
%%     click node2 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:249:249"
%%     node1 -->|"Yes"| node3{"Does local file 'filename' exist and match 'size'?"}
%%     click node3 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:251:253"
%%     node3 -->|"Yes"| node4["Return local file"]
%%     click node4 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:253:253"
%%     node3 -->|"No"| node5["Download 'filename' from <SwmToken path="monkey/infection_monkey/control.py" pos="248:7:7" line-data="        if not WormConfiguration.current_server:">`current_server`</SwmToken>"]
%%     click node5 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:255:258"
%%     node5 --> node6
%%     subgraph loop1["For each chunk in the download"]
%%       node6["Write chunk to local file"]
%%       click node6 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:261:263"
%%     end
%%     loop1 --> node7{"Does downloaded file match 'size'?"}
%%     click node7 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:265:265"
%%     node7 -->|"Yes"| node8["Return local file"]
%%     click node8 openCode "<SwmPath>[monkey/infection_monkey/control.py](monkey/infection_monkey/control.py)</SwmPath>:266:266"
%%     node7 -->|"No"| node2
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

This section ensures that the Infection Monkey agent uses a valid executable file by first checking for an existing local copy that matches the expected size. If the file is not present or does not match, it attempts to download the correct version from the control server, ensuring the agent always operates with the correct executable.

| Category        | Rule Name                        | Description                                                                                                                                             |
| --------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data validation | Control server required          | If no control server is configured, the process must not attempt to retrieve or download the executable and must return no result.                      |
| Data validation | Verify downloaded file size      | After downloading, the process must verify that the downloaded file matches the expected size before using it.                                          |
| Business logic  | Use valid local file             | If a local file with the specified filename exists and its size matches the expected value, the process must use this file and not attempt a download.  |
| Business logic  | Download missing or invalid file | If the local file does not exist or does not match the expected size, the process must attempt to download the file from the configured control server. |

<SwmSnippet path="/monkey/infection_monkey/control.py" line="247">

---

In <SwmToken path="monkey/infection_monkey/control.py" pos="247:3:3" line-data="    def download_monkey_exe_by_filename(filename, size):">`download_monkey_exe_by_filename`</SwmToken>, we first check if the executable is already present and matches the expected size using monkeyfs. If it's there and correct, we use it; otherwise, we need to fetch it.

```python
    def download_monkey_exe_by_filename(filename, size):
        if not WormConfiguration.current_server:
            return None
        try:
            dest_file = monkeyfs.virtual_path(filename)
            if (monkeyfs.isfile(dest_file)) and (size == monkeyfs.getsize(dest_file)):
                return dest_file
            else:
```

---

</SwmSnippet>

<SwmSnippet path="/monkey/infection_monkey/monkeyfs.py" line="44">

---

<SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="44:2:2" line-data="def isfile(path):">`isfile`</SwmToken> checks if the path starts with <SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="45:7:7" line-data="    if path.startswith(MONKEYFS_PREFIX):">`MONKEYFS_PREFIX`</SwmToken>. If it does, it delegates to <SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="46:3:3" line-data="        return VirtualFile.isfile(path)">`VirtualFile`</SwmToken> for virtual file checks; otherwise, it uses the standard <SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="48:3:7" line-data="        return os.path.isfile(path)">`os.path.isfile`</SwmToken> for real files. This lets us handle both virtual and real filesystems without changing the calling code.

```python
def isfile(path):
    if path.startswith(MONKEYFS_PREFIX):
        return VirtualFile.isfile(path)
    else:
        return os.path.isfile(path)
```

---

</SwmSnippet>

<SwmSnippet path="/monkey/infection_monkey/control.py" line="255">

---

Back in <SwmToken path="monkey/infection_monkey/control.py" pos="220:5:5" line-data="        return ControlClient.download_monkey_exe_by_filename(filename, size)">`download_monkey_exe_by_filename`</SwmToken>, if the file check fails, we download the executable from the control server and write it out chunk by chunk to the destination using monkeyfs.

```python
                download = requests.get("https://%s/api/monkey/download/%s" %  # noqa: DUO123
                                        (WormConfiguration.current_server, filename),
                                        verify=False,
                                        proxies=ControlClient.proxies)

                with monkeyfs.open(dest_file, 'wb') as file_obj:
                    for chunk in download.iter_content(chunk_size=DOWNLOAD_CHUNK):
                        if chunk:
                            file_obj.write(chunk)
```

---

</SwmSnippet>

<SwmSnippet path="/monkey/infection_monkey/control.py" line="263">

---

After writing the file, we use <SwmToken path="monkey/infection_monkey/control.py" pos="265:7:9" line-data="                if size == monkeyfs.getsize(dest_file):">`monkeyfs.getsize`</SwmToken> to confirm the file size matches what we expect. If it does, we return the path; otherwise, we don't.

```python
                            file_obj.write(chunk)
                    file_obj.flush()
                if size == monkeyfs.getsize(dest_file):
                    return dest_file

        except Exception as exc:
            LOG.warning("Error connecting to control server %s: %s",
                        WormConfiguration.current_server, exc)
```

---

</SwmSnippet>

<SwmSnippet path="/monkey/infection_monkey/monkeyfs.py" line="37">

---

<SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="37:2:2" line-data="def getsize(path):">`getsize`</SwmToken> checks the path prefix and decides whether to use <SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="39:3:5" line-data="        return VirtualFile.getsize(path)">`VirtualFile.getsize`</SwmToken> for virtual files or <SwmToken path="monkey/infection_monkey/monkeyfs.py" pos="41:3:5" line-data="        return os.stat(path).st_size">`os.stat`</SwmToken> for real files. This lets us handle both file types without extra logic elsewhere.

```python
def getsize(path):
    if path.startswith(MONKEYFS_PREFIX):
        return VirtualFile.getsize(path)
    else:
        return os.stat(path).st_size
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBYmFja2VuZC1zd2ltbSUzQSUzQXJpY2FyZG9sb3Blemc=" repo-name="backend-swimm"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
