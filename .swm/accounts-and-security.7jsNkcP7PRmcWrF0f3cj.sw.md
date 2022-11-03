---
id: 7jsNkcP7PRmcWrF0f3cj
name: Accounts and Security
file_version: 1.0.1
app_version: 0.6.0-0
file_blobs:
  monkey/monkey_island/cc/server_config.json.default: ecc4c17085f541544c205ceb688b7b2f7475db4b
---

## Security in Infection Monkey

The first time you launch Monkey Island (Infection Monkey CC server), you'll be prompted to create an account and secure your island. After your account is created, the server will only be accessible via the credentials you chose.

If you want island to be accessible without credentials press *I want anyone to access the island*. Please note that this option is insecure: you should only pick this for use in development environments.

## Resetting account credentials

To reset credentials edit `monkey_island\cc\server_config.json` by deleting `user` and `password_hash` variables. Then restart the Monkey Island server and you should be prompted with registration form again.

Example `server_config.json` for account reset:

```json
{
  "server_config": "password",
  "deployment": "develop"
}
```

<br/>

You can look at this example that comes with the repo:
<!-- NOTE-swimm-snippet: the lines below links your snippet to Swimm -->
### 📄 monkey/monkey_island/cc/server_config.json.default
```default
🟩 1      {
🟩 2        "server_config": "password",
🟩 3        "deployment": "develop"
🟩 4      }
⬜ 5      
```

<br/>



<br/>

This file was generated by Swimm. [Click here to view it in the app](https://swimm.io/link?l=c3dpbW0lM0ElMkYlMkZyZXBvcyUyRlpnMWZscldSZ3ZsczBjMm1GeURJJTJGZG9jcyUyRjdqc05rY1A3UFJtY1dyRjBmM2Nq). Timestamp: 2021-10-14T15:25:52.242Z (UTC)