---
id: ipk25
name: How to make something configurable
file_version: 1.0.2
app_version: 0.9.6-1
file_blobs:
  monkey/infection_monkey/config.py: 1fbcb876bb4589c9d2cbb22168b4d8e14f7177cc
  monkey/monkey_island/cc/services/config_schema/internal.py: bdbae24615730e417ba7421e95358c09e9e2d3a0
---

Start by adding your configuration, in this example, we added `victims_max_find`[<sup id="Z9sEYN">↓</sup>](#f-Z9sEYN)
<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/infection_monkey/config.py
```python
⬜ 134        # how many victims to look for in a single scan iteration
⬜ 135        victims_max_find = 100
⬜ 136    
🟩 137        # how many victims to exploit before stopping
🟩 138        victims_max_exploit = 100
⬜ 139    
⬜ 140        # depth of propagation
⬜ 141        depth = 2
```

<br/>

Then, add the same flag, in this case `victims_max_exploit`[<sup id="Z2peYDi">↓</sup>](#f-Z2peYDi) configuration here
<!-- NOTE-swimm-snippet: the lines below link your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/services/config_schema/internal.py
```python
⬜ 46                         "default": 100,
⬜ 47                         "description": "Determines the maximum number of machines the monkey is allowed to scan"
⬜ 48                     },
🟩 49                     "victims_max_exploit": {
🟩 50                         "title": "Max victims to exploit",
🟩 51                         "type": "integer",
🟩 52                         "default": 100,
🟩 53                         "description":
🟩 54                             "Determines the maximum number of machines the monkey"
🟩 55                             " is allowed to successfully exploit. " + WARNING_SIGN
🟩 56                             + " Note that setting this value too high may result in the monkey propagating to "
🟩 57                               "a high number of machines"
🟩 58                     },
⬜ 59                     "internet_services": {
⬜ 60                         "title": "Internet services",
⬜ 61                         "type": "array",
```

<br/>

<!-- THIS IS AN AUTOGENERATED SECTION. DO NOT EDIT THIS SECTION DIRECTLY -->
### Swimm Note

<span id="f-Z2peYDi">victims_max_exploit</span>[^](#Z2peYDi) - "monkey/monkey_island/cc/services/config_schema/internal.py" L49
```python
                "victims_max_exploit": {
```

<span id="f-Z9sEYN">victims_max_find</span>[^](#Z9sEYN) - "monkey/infection_monkey/config.py" L135
```python
    victims_max_find = 100
```

<br/>

This file was generated by Swimm. [Click here to view it in the app](https://app.swimm.io/repos/Zg1flrWRgvls0c2mFyDI/docs/ipk25).