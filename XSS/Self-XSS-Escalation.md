# <u>Self- XSS Explanation and Escalation</u>



## What's that?

- XSS only in context of the Payload executer (Attacker == Victim). No links to share, No common Endpoint to visit, etc.



## How to?

- **POST to GET :** Find the common param being used in both GET and POST requests

- **XSS + CSRF :** Possible to logout the current user and log them in using the account we create and control with the XSS Trigger. Eg.

  <iframe src=LOGOUT_URL onload=forms[0].submit()> </iframe>
  <form method=post action=LOGIN_URL> 
      <input name=USERNAME_PARAMETER_NAME value=USERNAME> 
      <input name=PASSWORD_PARAMETER_NAME value=PASSWORD>

  - CON: The user might see the contents of the Vulnerable page

- **Chain with Other Bugs : ** Can abuse CSP and SOP?, Oauth flow issues?, Iframe/Clickjacking/UI Redressing Protection?, X-Frame-Header/Options?, etc.











