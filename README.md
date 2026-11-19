# Website Error Fixes

Quick, practical fixes for common browser, DNS and SSL errors. Each entry gives the meaning in one line, the fastest fixes, and a link to a full step-by-step guide.

Maintained by [Web Tools Realm](https://webtoolsrealm.com), free website and network diagnostic tools.

## Contents

- [DNS_PROBE_FINISHED_NXDOMAIN](#dns_probe_finished_nxdomain)
- [DNS_PROBE_FINISHED_BAD_CONFIG](#dns_probe_finished_bad_config)
- [ERR_SSL_PROTOCOL_ERROR](#err_ssl_protocol_error)
- [SSL_ERROR_RX_RECORD_TOO_LONG (Firefox)](#ssl_error_rx_record_too_long-firefox)
- [NET::ERR_CERT_COMMON_NAME_INVALID](#neterr_cert_common_name_invalid)
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

## DNS_PROBE_FINISHED_BAD_CONFIG

**Meaning:** The DNS settings on your own device or network are broken. It has nothing to do with the website.

**First test:** If every site fails, the problem is local. If only one site fails, check NXDOMAIN instead.

**Fixes:**

```
ipconfig /flushdns
netsh winsock reset
```

- Restart the router
- Disconnect any VPN and check the adapter is not still using its DNS servers
- Set DNS manually to `1.1.1.1` or `8.8.8.8`

Full guide: https://webtoolsrealm.com/blog/dns-errors-explained

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

## SSL_ERROR_RX_RECORD_TOO_LONG (Firefox)

**Meaning:** Firefox opened an HTTPS connection, but the server answered with something that was not TLS. Most often the server is sending plain HTTP on port 443. Chrome usually shows the same problem as ERR_SSL_PROTOCOL_ERROR.

**Check:**

```
openssl s_client -connect example.com:443 -servername example.com
```

If the output shows HTML or `wrong version number`, the server is not speaking TLS on that port.

**Fixes:**

- Nginx: use `listen 443 ssl;`, not just `listen 443;`
- Apache: enable `mod_ssl` and add `SSLEngine on` in the `<VirtualHost *:443>` block
- Remove port numbers like `:80` from `https://` URLs

Full guide: https://webtoolsrealm.com/blog/err-ssl-protocol-error

---

## NET::ERR_CERT_COMMON_NAME_INVALID

**Meaning:** The certificate is valid, but it was not issued for the hostname you visited.

**Check which names the certificate covers:**

```
openssl s_client -connect example.com:443 -servername example.com | openssl x509 -noout -ext subjectAltName
```

**Common causes:** the certificate covers `example.com` but not `www.example.com`, a wildcard only covers one level, or the server returns another site's default certificate.

**Fix:**

```
certbot -d example.com -d www.example.com
```

Full guide: https://webtoolsrealm.com/blog/ssl-certificate-errors-explained

Check any certificate online: https://webtoolsrealm.com/tool/ssl-checker

---

## SERVFAIL

**Meaning:** The resolver tried and could not complete the lookup. Usually DNSSEC validation failed or the nameservers are unreachable.

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
- Website Status Checker: https://webtoolsrealm.com/tool/website-status-checker

Suggestions and corrections are welcome. Open an issue.
