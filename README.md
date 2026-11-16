# Website Error Fixes

Quick, practical fixes for common browser, DNS and SSL errors. Each entry gives the meaning in one line, the fastest fixes, and a link to a full step-by-step guide.

Maintained by [Web Tools Realm](https://webtoolsrealm.com), a free website and network diagnostic tool.

## Contents

- [DNS_PROBE_FINISHED_NXDOMAIN](#dns_probe_finished_nxdomain)
- [ERR_SSL_PROTOCOL_ERROR](#err_ssl_protocol_error)
- [SERVFAIL](#servfail)
- [Connection refused vs connection timed out](#connection-refused-vs-connection-timed-out)

---

## DNS_PROBE_FINISHED_NXDOMAIN

**Meaning:** The DNS resolver says the domain name does not exist, so Chrome cannot find the site's IP address.

**First test:** Open the site on mobile data. If it loads there, the problem is your device or network.

**Fixes:**

```
chrome://net-internals/#dns        -> Clear host cache
ipconfig /flushdns                 (Windows)
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder   (macOS)
```

- Switch DNS to `1.1.1.1` or `8.8.8.8`
- Disable VPN, ad blocker or DNS filter and test again

Full guide: https://webtoolsrealm.com/blog/dns-probe-finished-nxdomain

---

## ERR_SSL_PROTOCOL_ERROR

**Meaning:** The TLS handshake failed before any certificate was checked. It is not a certificate problem.

**First test:** Try another device on the same network. Works there? The cause is local, usually antivirus HTTPS scanning.

**Check the server:**

```
openssl s_client -connect example.com:443 -servername example.com
```

- `wrong version number`: the server is serving plain HTTP on port 443
- Handshake completes: the server is fine, the client is the problem

Full guide: https://webtoolsrealm.com/blog/err-ssl-protocol-error

---

## SERVFAIL

**Meaning:** the resolver tried and could not complete the lookup. Usually DNSSEC validation failed or the nameservers are unreachable.

**Check:**

```
dig example.com
dig +cd example.com
```

If the second command works and the first fails, the cause is DNSSEC, not the records.

Full guide: https://webtoolsrealm.com/blog/servfail-explained

---

## Connection refused vs connection timed out

| Error | What happened | Usual cause |
|---|---|---|
| Connection refused | Server replied and rejected it (fast) | Service not running, wrong port, bound to 127.0.0.1 |
| Connection timed out | No reply at all (slow) | Firewall dropping packets, NAT, ISP block |

**Check what is listening on the server:**

```
ss -tlnp
```

Full guide: https://webtoolsrealm.com/blog/connection-refused-vs-connection-timed-out

---

## Free tools

- DNS Lookup: https://webtoolsrealm.com/tool/dns-lookup
- SSL Checker: https://webtoolsrealm.com/tool/ssl-checker
- Open Port Checker: https://webtoolsrealm.com/tool/open-port-checker
- Redirect Checker: https://webtoolsrealm.com/tool/redirect-checker

Suggestions and corrections are welcome. Open an issue.
