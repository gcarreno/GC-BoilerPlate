# Miscellaneous

## GetMyIP

Here’s a **comprehensive list** of popular and reliable websites/services you can use to get your **public-facing IP address** (IPv4 and/or IPv6). I’ll organize them into two buckets:

---

### Plaintext / API-style responses

(These are curl-friendly — return **just** the IP, no HTML fluff.)

| Service                                                                    | Notes                              | Example curl                         |
| -------------------------------------------------------------------------- | ---------------------------------- | ------------------------------------ |
| **[https://api.ipify.org](https://api.ipify.org)**                         | Very minimal, works with IPv4/IPv6 | `curl https://api.ipify.org`         |
| **[https://ifconfig.me](https://ifconfig.me)**                             | Returns IP by default              | `curl ifconfig.me`                   |
| **[https://icanhazip.com](https://icanhazip.com)**                         | From DNSimple, very popular        | `curl icanhazip.com`                 |
| **[https://ident.me](https://ident.me)**                                   | Super clean, supports IPv6         | `curl https://ident.me`              |
| **[https://checkip.amazonaws.com](https://checkip.amazonaws.com)**         | From AWS — simple                  | `curl https://checkip.amazonaws.com` |
| **[https://ipinfo.io/ip](https://ipinfo.io/ip)**                           | Returns only IP if you hit `/ip`   | `curl ipinfo.io/ip`                  |
| **[https://wtfismyip.com/text](https://wtfismyip.com/text)**               | Fun name, serious answers 😄       | `curl wtfismyip.com/text`            |
| **[https://ip.42.pl/raw](https://ip.42.pl/raw)**                           | Plain IP in raw text               | `curl ip.42.pl/raw`                  |
| **[https://myexternalip.com/raw](https://myexternalip.com/raw)**           | Another clean one                  | `curl myexternalip.com/raw`          |
| **[https://bot.whatismyipaddress.com](https://bot.whatismyipaddress.com)** | IP-only variant                    | `curl bot.whatismyipaddress.com`     |

---

### HTML/JSON-heavy responses

(Useful for APIs or human-readable formats, but not for piping into scripts.)

| Service                                                            | Format | Notes                            |
| ------------------------------------------------------------------ | ------ | -------------------------------- |
| **[https://ipinfo.io](https://ipinfo.io)**                         | JSON   | Great metadata (org, city, etc.) |
| **[https://ip-api.com/json](https://ip-api.com/json)**             | JSON   | Also shows geolocation           |
| **[https://ipwho.is](https://ipwho.is)**                           | JSON   | Open API, lots of data           |
| **[https://ipapi.co/json](https://ipapi.co/json)**                 | JSON   | Decent free tier                 |
| **[https://geoip-db.com/json](https://geoip-db.com/json)**         | JSON   | Also location aware              |
| **[https://freegeoip.app/json/](https://freegeoip.app/json/)**     | JSON   | Free GeoIP data                  |
| **[https://whatismyipaddress.com](https://whatismyipaddress.com)** | HTML   | Good for browsing                |
| **[https://www.whatsmyip.org](https://www.whatsmyip.org)**         | HTML   | Basic + tools                    |

---

### IPv6-specific or fallback-aware

Some services respond with your IPv6 if available, or fall back to IPv4.

You can **force IPv4 or IPv6** with curl if needed:

```bash
# Force IPv4:
curl -4 https://icanhazip.com

# Force IPv6:
curl -6 https://icanhazip.com
```

---

### Bonus: dig / drill (DNS queries)

No need for HTTP at all:

```bash
dig +short myip.opendns.com @resolver1.opendns.com

# Or:
drill myip.opendns.com @resolver1.opendns.com
```
