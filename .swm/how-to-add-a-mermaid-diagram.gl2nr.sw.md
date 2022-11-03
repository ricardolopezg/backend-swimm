---
id: gl2nr
name: How to Add a Mermaid Diagram.
file_version: 1.0.2
app_version: 0.9.8-4
file_blobs:
  monkey/infection_monkey/model/victim_host_generator.py: 1e9eba9c2ebb8163b0e325a1fd0f72b4cadba533
  monkey/monkey_island/cc/ui/src/components/ui-components/DropdownSelect.js: 8628c0b607351b9562abd17ac4cd20f68070f0f9
---

<!--MERMAID {width:100}-->
```mermaid
graph TD
A[generate_victims] -->|Clicks Link| B(Landing Page)
B --> C{onClick}
C -->|One| D[Harvest Data]
C -->|Two| E[Phish]
C -->|Three| F[Exploit]```
<!--MCONTENT {content: graph TD
A\[`generate_victims`[<sup id="Z1TbLj9">↓</sup>](#f-Z1TbLj9)\] \-\-\>|Clicks Link| B(Landing Page)
B \-\-\> C{`onClick`[<sup id="1AAyRO">↓</sup>](#f-1AAyRO)}
C \-\-\>|One| D\[Harvest Data\]
C \-\-\>|Two| E\[Phish\]
C \-\-\>|Three| F\[Exploit\]} --->

<br/>

<!-- THIS IS AN AUTOGENERATED SECTION. DO NOT EDIT THIS SECTION DIRECTLY -->
### Swimm Note

<span id="f-Z1TbLj9">generate_victims</span>[^](#Z1TbLj9) - "monkey/infection_monkey/model/victim_host_generator.py" L10
```python
    def generate_victims(self, chunk_size):
```

<span id="f-1AAyRO">onClick</span>[^](#1AAyRO) - "monkey/monkey_island/cc/ui/src/components/ui-components/DropdownSelect.js" L54
```javascript
  onClick: PropTypes.func
```

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Z2l0aHViJTNBJTNBYmFja2VuZC1zd2ltbSUzQSUzQXJpY2FyZG9sb3Blemc=/docs/gl2nr).