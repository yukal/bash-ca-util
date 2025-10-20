# bash-ca-util
A tiny bash utility for creating certificates. Useful for development on a local machine.

### Usage

```bash
Usage: cautil <command> [arguments]

Commands:
  init                          – Initialize CA and create initial CRL.
  server <domain>               – Generate server certificate (e.g. osbb.loc).
  client <name>                 – Generate client certificate (e.g. client1).
  revoke <SN>                   – Revoke a certificate (irreversible).
  list <role>?                  – Show list of certificates (role: server|client).
  find <CN>                     – Find certificate (e.g. osbb.loc).
  check <CN>?                   – Run validation scenario for all or one certificate 
                                  (e.g. osbb.loc).
  deploy <domain>?              – Deploy generated certificates into the project and 
                                  update trusted CA.
  test <client_name> <domain>   – Test connection using generated certificates.
  demo                          – Run full test scenario and cleanup.

  <arg>? – optional      (command runs with optional argument)
  *CN    – Common Name   (certificate field)
  *SN    – Serial Number (certificate serial number)
```
