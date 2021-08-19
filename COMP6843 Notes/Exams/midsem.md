# midsem1

1. Flag can be found in source code comment
2. Cookie structure false:md5(false) -> change false to true, flag can be found in response header
3. /robots.txt reveals /quack only allowed for user agent Quoccrome - access /quack with modified user-agent

# midsem2

Burp Suite change price param

# midsem3

/robots.txt reveals /super-secret-backup.zip (even though disallowed)

index.old in zip file is the source code for the website -> revealing that password is just the md5 hash of username

admin, md5(admin) -> intercept with Burp Suite before being redirected to get flag

# midsem4

Guess login: admin, admin

OTP: calls /api/verify_code/111111

Tells you which digit is wrong in response - brute force to obtain correct code and login to get the flag

# midsem5

Observations:

- /raw/base64encoded id three times
  - `b64encode(b64encode(b64encode(id)))`
- b64decoded param vulnerable to SQLi
- First file starts from id=4
- id=0 returns 404
- Only one row is returned at a time: `0' union select table_name, column_name from information_schema.columns-- )` 
- 'collations' table has columns id, collation_name, character_set_name, is_default, is_compiled, sortlen

Flags:

- /raw/encoded(2) returns flag
- /raw/encoded(3) returns a 403 error -> suggests accessing it in a different way
  - replacing 3 with `0' or id=3-- )` returns the flag
- /raw/encoded(1) is a test file - did not get flag



