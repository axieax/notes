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

Cookies:

- Cookies follow same-site rules not same-origin
- Security attributes are aligned to same-site rules
- HttpOnly - allow/deny JS accessing cookie
- Secure - set/send cookie through TLS (https)
- SameSite - send/block cookie to cross-site
  - Strict - most defensive option
    - https://b.com -> https://a.com (no cookie for a.com sent)
  - Lax - most flexible option
    - https://b.com -> https://a.com (cookie sent if top nav)
      - Only GET request
      - No JS request
  - None - cookies sent all the time
  - **Note: this does not block for same site**

Same Origin Policy (SOP):

- All cross-origin calls must return with Access-Control-* headers:
  - Access-Control-Allow-Origin: list of origins allowed
  - Access-Control-Allow-Methods: list of methods allowed
  - Access-Control-Allow-Headers: list of non-standard headers
  - Access-Control-Max-Age: value in secs to cache preflight request
- GET request to cross-origin - no cookies sent
- POST request through forms or image GET - cookies sent, but response not visible
  - CSRF if method not checked - i.e. GET request
- JSONP



**CSRF** - expected requests include a pseudo-random CSRF token, which is difficult to be forged by an attacker

e.g. ensures that requests to a specific endpoint can only be used as intended (e.g. forms with hidden value for csrf value)

**XSS** - `<scRipt>`

CSP against XSS, Clickjacking



If DOM already rendered, further inject `<img src=x onerror=alert(1)>` instead of `<script>alert(1)</script>`.



