# Week 0

1. welcome subdomain
2. welcome subdomain
3. account subdomain - inspect element: edit no to yes, set checked="true" on checkbox
4. cookies subdomain - edit the value of the cookie lucky from 0 to 1
5. card subdomain - intercept form submission with Burp Suite, change fee value, forward

# Week 1

Task: find as many subdomains as you can by using reconnaissance

1. crt.sh to find super-secret.admin subdomain

2. m.staging subdomain

3. vault42.sandbox subdomain

4. careers page from www subdomain sitemap (or bruteforce)

5. subdomainnfinder.c99.nl to find test subdomain

6. wow-how-did-i-find-this-super-secret-backup subdomain

7. mobile subdomain

8. m subdomain

9. banking subdomain

10. dev subdomain

11. Page source of www subdomain to find www-prepod subdomain, or pentest-tools or script

12. pentest-tools or script to find adserver subdomain

13. HTTP as a service challenge 1, access kb.quoccabank.com via haas.quoccabank.com with

    ```http
    GET / HTTP/1.1
    Host: kb.quoccabank.com
    ```

    returns

    ```http
    HTTP/1.1 200 OK
    Accept-Ranges: bytes
    Content-Length: 249
    Content-Type: text/html; charset=utf-8
    Date: Fri, 11 Jun 2021 01:37:57 GMT
    Server: CTFProxy/2aea9b0
    X-Ctfproxy-Trace-Context: a4692045-e690-4bd0-a745-95dccb53e3c8
    Connection: close
    
    <a href="/c98efc0d-5c3f-45ec-996a-2cb82d35ed26.html">follow this link to get easy flag (manual work)</a>
    <a href="/deep/">follow this link to get harder flag (automatic work)</a>
    <a href="/calculator/">follow this link to visit human calculator</a>
    ```

    Changing request to:

    ```http
    GET /c98efc0d-5c3f-45ec-996a-2cb82d35ed26.html HTTP/1.1
    Host: kb.quoccabank.com
    ```

    returns

    ```http
    HTTP/1.1 200 OK
    Accept-Ranges: bytes
    Content-Length: 99
    Content-Type: text/html; charset=utf-8
    Date: Fri, 11 Jun 2021 01:39:00 GMT
    Server: CTFProxy/2aea9b0
    X-Ctfproxy-Trace-Context: 3d66a696-1403-4c3b-b724-825b251e6bab
    Connection: close
    
    <a href="/7643dc3d-2262-4f1c-8fb9-197860946a66.html">7643dc3d-2262-4f1c-8fb9-197860946a66.html</a>
    ```

    Do something similar with the new link to obtain flag

14. HTTP as a service challenge 2 (deep), accessing with

    ```http
    GET /deep/ HTTP/1.1
    Host: kb.quoccabank.com
    ```

    returns

    ```http
    HTTP/1.1 200 OK
    Accept-Ranges: bytes
    Content-Length: 787
    Content-Type: text/html; charset=utf-8
    Date: Sat, 12 Jun 2021 11:37:56 GMT
    Server: CTFProxy/2aea9b0
    X-Ctfproxy-Trace-Context: 0b197c0d-5d50-4f3b-ab20-f2467f6bb19f
    
    kwqaduor<a href="./opnvlqckyaubifmp.html">mgykdpwbg</a>blaymmmfbzmrnujy<a href="./oeyarrefx.html">wyzfm</a>xdzpryqiosk<a href="./xkeqdjazwjvtbobszre.html">vqdvdufaj</a>goynmcllr<a href="./oyboldbqcpt.html">smt</a>zyjyamfsnmge<a href="./iepehreaqehpsrnjzb.html">yphwgtwdh</a>vihuvpvefgoah<a href="./effbdctwzpn.html">tygzi</a>msdtrkxvpzwgs<a href="./wolkdbnjakwvzot.html">rtfmvbkp</a>ngtadacutpg<a href="./kyvdwvthwq.html">mykzmti</a>mfoaxog<a href="./kbhppvjemarq.html">yrcrncyqi</a>kgoulmjdey<a href="./gpoqy.html">xdr</a>vjigvsreope<a href="./wdbjqlzqw.html">qqyinjbc</a>mbisppgqxjnyy<a href="./gqtntyzilmvd.html">dbpknno</a>sfqsqqvv<a href="./xxkilksedch.html">jayehrgif</a>mphvduqypasoh<a href="./ijbcptcicsgtqqv.html">ybnn</a>xpuptwwnsicnb<a href="./pvzgbhteg.html">xmrppg</a>ubhjpn
    ```

    where each link returns something similar. I used Python to make a request to each subpage through haas recursively:

    ```python
    ''' Make recursive requests to kb.quoccabank.com via haas.quoccabank.com '''
    seen = set()
    def solve(sub='', throttle=0):
        # keep track of seen substitutions
        if sub in seen:
            return
        seen.add(sub)
        # make request
        payload = {
            'request': f'GET /deep/{sub} HTTP/1.1\nHost: kb.quoccabank.com'
        }
        resp = requests.post('https://haas.quoccabank.com/', cert=(cert_path, key_path), data=payload)
        time.sleep(throttle)
        if resp.status_code == 420:
            # make the same request again
            seen.remove(sub)
            solve(sub, throttle + 0.1)
        else: 
            print(resp.text)
            # find and recursively request other links
            links = [x[2:] for x in re.findall(r'<a href="(.*?)"', resp.text)]
            for link in links:
                solve(link, throttle)
    
    if __name__ == '__main__':
        # python deep.py | grep COMP6443
        solve()
    ```
    
15. HTTP as a service challenge 3 (calculator) - get request with

    ```http
    GET /calculator/ HTTP/1.1
    Host: kb.quoccabank.com
    ```

    returns something like

    ```http
    HTTP/1.1 200 OK
    Content-Length: 264
    Content-Type: text/html
    Date: Sat, 12 Jun 2021 11:41:06 GMT
    Server: CTFProxy/2aea9b0
    Set-Cookie: calc=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJQcm9ncmVzcyI6MCwiTGFzdEFuc3dlciI6MTMyLCJTdGFydFRpbWUiOjE2MjM0OTgwNjZ9.YR2zkik4MCP63ie2OXleKTugHwyjZ-TUqI8x5dL0dWU; HttpOnly
    X-Ctfproxy-Trace-Context: 4353b0b8-28ab-4b70-9f63-db56514d23c1
    
    <h1>welcome to calculator</h1>You are the calculator! If you answer 20 questions correctly in 2 minutes, you can proceed<br>You have currently answered 0 questions.<br>What is 50+82?<form method="POST"><input name="answer" type="text"/><input type="submit"></form>
    ```

    We need to make POST requests with answer= the solved mathematical question, and carrying the cookie returned each time. I used the following Python script:

    ```python
    ''' Make recursive requests to kb.quoccabank.com via haas.quoccabank.com '''
    def solve(prev_answer=0, prev_cookie='', throttle=0):
        # make request
        answer = 'answer=' + str(prev_answer)
        data = {'request': '''POST /calculator/ HTTP/1.1
    Host: kb.quoccabank.com''' + ('\n' + prev_cookie if prev_cookie else '') + f'''
    Content-Type: application/x-www-form-urlencoded
    Content-Length: {len(answer)}
    
    {answer}
    '''}
        resp = requests.post('https://haas.quoccabank.com/', cert=(cert_path, key_path), data=data)
        time.sleep(throttle)
        if resp.status_code == 420:
            # make the same request again
            solve(prev_answer, prev_cookie, throttle + 0.1)
        else: 
            print(resp.text)
            try:
                # extract cookie and compute equation
                cookie = re.findall(r'Set-(Cookie: calc=.*?);', resp.text)[0]
                eqn = re.findall(r'What is (.*?)\?', resp.text)[0]
                solve(eval(eqn), cookie, throttle)
            except IndexError:
                pass
    
    if __name__ == '__main__':
        # python calculator.py | grep COMP6443
        solve()
    ```
    
16. Subdomain bruteforce script - creditcard

17. www-dev

18. Amass to find www-staging subdomain

19. www-cdn

20. 



<u>Notes:</u>

- Find subdomains using cert.sh, pentest-tools.com, subdomainnfinder.c99.nl, Amass or script:

  ```python
  def solve(subdomain, throttle=0):
      # make request
      print(f'\n\nSubdomain: {subdomain}')
      try:
          resp = requests.get(f'https://{subdomain}.quoccabank.com/', cert=(cert_path, key_path))
          print(resp.text)
          if 'COMP6443{' in resp.text:
              sys.stderr.write(f'Found: {subdomain}\n')
              sys.stderr.write(resp.text)
              sys.stderr.write('\n')
      except Exception as e:
          print(f'Not found: {e}')
      print('\n======================')
  
  if __name__ == '__main__':
      # python subdomain.py > subdomains.txt
      with open('subdomain_big.txt') as f:
          wordlist = set(f.read().lower().split())
      for word in wordlist:
          solve(word)
  ```
  
- Burp Suite - install extension Copy As Python - Requests --> right click intercepted request and select copy to Python

