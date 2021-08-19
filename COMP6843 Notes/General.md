Burp Suite - look for anomaly (e.g. status code, response length)

Script requests/curl through --proxy http://127.0.0.1:8080 -k to allow insecure as well

python3 -u script.py | tee file_name

buggy web app - bwapp

Some fields may not be validated server side - try empty or dummy values

Send data via different HTTP method - CSRF

Try open script tag - break

CSRF - impersonating a client - cookie

SSRF - impersonating a server - accessing internal resources

- Request header host - 127.0.0.1:PORT
- Exposed router can be hacked and used to access local resources
- robots.txt doesn't enforce disallowed access to sites
- /etc/hosts/ demo - can specify external addresses, vs internal 127.0.0.1

Prevent XSS with intended HTML injection using iframes - sandbox site for XSS

- Sandbox escape to trigger XSS on main site could still be possible tho (advanced)
- CSP

==CHECK COOKIES==

==CHECK DIFFERENT METHOD==

==If DOM already rendered, further inject `<img src=x onerror=alert(1)>` instead of `<script>alert(1)</script>`==

