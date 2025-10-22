# bash-util-ca
A tiny bash utility for managing certificates. Useful for development on a local machine.

## Table of Contents
- [Features](#features)
- [Usage](#usage)
- [Setup](#setup)
- [Structure](#structure)


## Features

- **Initialize** your own Certificate Authority (CA).
- **Generate** a server certificate.
- **Generate** a client certificate.
- **Revoke** a certificate.
- **View** the list of generated certificates.
- **Find** a certificate by its Common Name (CN).
- **Validate** one or all generated certificates.
- **Deploy** certificates and update trusted CA.
- **Test** an HTTPS connection to a resource using the appropriate certificate.


## Usage

```bash
Usage: utilca <command> [arguments]

Commands:
  init                          – Initialize CA and create initial CRL.
  server <domain>               – Generate server certificate (e.g. domain.loc).
  client <name>                 – Generate client certificate (e.g. client1).
  revoke <SN>                   – Revoke a certificate (irreversible).
  list <role>?                  – Show list of certificates (role: server|client).
  find <CN>                     – Find certificate (e.g. domain.loc).
  check <CN>?                   – Run validation scenario for all or one certificate 
                                  (e.g. domain.loc).
  deploy <domain>?              – Deploy generated certificates into the project and 
                                  update trusted CA.
  test <client_name> <domain>   – Test connection using generated certificates.
  demo                          – Run full test scenario and cleanup.

  <arg>? – optional      (command runs with optional argument)
  *CN    – Common Name   (certificate field)
  *SN    – Serial Number (certificate serial number)
```


## Setup

You can run this utility from a **specific directory** where the script is located. In this case, you'll need minimal changes to configure it for your needs, apart from those related to your OS specifics.

You might need to configure the `deploy` command. If you plan to use this functionality, you must specify the path to your **trusted CA certificates directory** in the environment variable `CA_TRUST_DIR` (for: [Fedora](https://docs.fedoraproject.org/en-US/quick-docs/using-shared-system-certificates/), [Ubuntu](https://documentation.ubuntu.com/server/how-to/security/install-a-root-ca-certificate-in-the-trust-store/), etc).

```bash
# For Fedora
CA_TRUST_DIR="/etc/pki/ca-trust/source/anchors"

# For Ubuntu
CA_TRUST_DIR="/usr/local/share/ca-certificates"
```

If you plan to execute this script from a global directory like `/usr/local/bin` or `/usr/bin`, then you should specify the directory where the CA files and generated certificates will be stored by replacing the variable value in the script:

```bash
APP_DIR="/chose/location/for/utilca"
```

### Local Domains Configuration

Since this utility is primarily for **local development**, you will likely need to configure your operating system's **`/etc/hosts`** file to resolve the domains you generate certificates for (e.g., `domain.loc`) to your local machine:

```bash
# Example /etc/hosts configuration for local domains

# Main domains
127.0.0.1   domain.loc
127.0.0.1   www.domain.loc

# Subdomains
127.0.0.1   img.domain.loc
127.0.0.1   dev.domain.loc
127.0.0.1   api.domain.loc
```

### Nginx Configuration Example

For guidance on how to set up your local web server to use the generated certificates, refer to the provided Nginx configuration file:

  * **`example.nginx.conf`**: Contains a complete [example](nginx/conf.d/example.domain.conf) showing how to configure Nginx for multiple HTTPS subdomains, including HTTP-to-HTTPS redirection, static file serving, proxying to a development server (Vite/Vue), and setting up **Client Certificate Authentication** for an API endpoint.


## Structure

The utility creates and manages the following file structure.

### Main Structure (`$APP_DIR`)

```
utilca
│
├─ ca
│  ├─ issued             // Issued and revoked certificates
│  ├─ crl                // Certificate Revocation List (CRL) index
│  │
│  ├─ ca.crt             // Root CA Certificate
│  ├─ ca.key             // CA Private Key
│  ├─ ca.pass            // Password of the private key
│  ├─ ca_signing.cnf     // CA Configuration
│  └─ ...
│
├─ client                // Client certificates
│  ├─ am-client
│  │  ├─ am-client.crt   // Certificate
│  │  ├─ am-client.csr   // Certificate Signing Request
│  │  ├─ am-client.ext   // Extensions config
│  │  ├─ am-client.key   // Private Key
│  │  ├─ am-client.p12   // PKCS#12 certificate (with password)
│  │  └─ am-client.pass  // Password to PKCS#12 certificate
│  ├─ ...
│
└─ server                // Server certificates
   ├─ domain.loc
   │  ├─ domain.loc.crt   // Certificate
   │  ├─ domain.loc.csr   // Certificate Signing Request
   │  ├─ domain.loc.ext   // Extensions config
   │  ├─ domain.loc.key   // Private Key
   ├─ ...
```

### Deployment Example (Target Project Structure)

The `deploy` command is intended to place certificates into your project structure, often into a dedicated directory like `.ssl`.

```
var
├─ www
    ├─ domain.loc      // Your domain/project
    │  ├─ .ssl         // Target directory for deployed certificates
    │  │  ├─ ca.crt    // Root CA copied here
    │  │  ├─ server.crt
    │  │  ├─ server.key
    │  │  └─ ...
    │  │
    │  ├─ img
    │  ├─ www
    │  └─ ...
    │
    ├─ domain2.loc     
    │  └─ .ssl
    │
    └─ ...
```
