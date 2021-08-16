# Same site vs same origin

scheme + hostname (thing + domain) + port

https://www.example.com:443

- Scheme: https
- Host name: www.example.com
  - eTLD: .com
  - eTLD+1: example.com
- Port: 443

https://axieax.github.io:443

- Scheme: https
- Host
  - eTLD: github.io (io not enough to identify site)
  - eTLD+1: axieax.github.io
- Port: 443

- SITE
  - Same site: Same eTLD+1
  - Cross origin: otherwise
- ORIGIN
  - Same origin: same scheme, hostname and port
  - Cross origin: otherwise (including different subdomain)

**CSRF** - expected requests include a pseudo-random CSRF token, which is difficult to be forged by an attacker

e.g. ensures that requests to a specific endpoint can only be used as intended (e.g. forms with hidden value for csrf value)

**XSS** - `<scRipt>`





