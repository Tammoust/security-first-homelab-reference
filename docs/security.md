# Security and Publication Rules

## Public repository policy

Only sanitized architecture, generic runbooks, and non-production examples belong here.

Never commit:

- credentials, tokens, SSH keys, certificates, cookies, or backup archives;
- live addresses, hostnames, MAC addresses, domain names, Wi-Fi identifiers, or service endpoints;
- router/firewall configuration, packet captures, raw logs, or monitoring exports;
- personally identifying information or provider-account details.

## Operational policy

- Review external exposure and UPnP/NAT mappings regularly.
- Give every exposed service an owner, purpose, and rollback path.
- Restrict management interfaces to trusted networks.
- Use least-privilege identities and rotate/revoke access when no longer required.
- Treat monitoring data as sensitive operational information.
