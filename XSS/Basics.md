- **Inside normal HTML tags** like <b> and <i> without any filtering - Direct Execution 

- **Inside an HTML attribute** - Close the current tag and add the payload ("><js-payload)

- **Input Field has proper escaping** - try some other places with predefined inputs (dropdown, checkbox, etc) and try injecting there using Burp or Inspect Element

- **Invisible Input Fields** - Check the Source code or Post request for any hidden parameters

- **Length Limit** - Bypass using Inspect Element or Burp

- **Inside an Attribute value and "<" & ">" filtered** -  Try to invoke any event handler (" onload=js-payload)

- **Inside and Attribute value with even double quotes filtered** - Try breaking the context using backtick `

  ```
  (` onmouseover=js-payload)		
  ```

- **Inside href tag making dynamic links** - Use the JavaScript directive (javascript:js-payload)





Reference:

Yamagata XSS