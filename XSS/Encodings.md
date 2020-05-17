# <u>Encodings used to bypass XSS Filters</u>

- HTML entities with ASCII codes

- Hexadecimal encoding can be used for ASCII codes

- Base64 encoding can be used to obfuscate attack code

- All encoded character entities can be from 1 to 7 numeric characters,  with initial zeroes being ignored, so any combinations of zero padding  are possible

  ```
  <a href="&#x6A;avascript&#0000058&#0000097lert('Successful XSS')">Click this link!</a>
  ```

- Character codes can be used to hide XSS payloads

- Tabs can be added and encoded

- Octal Encoding

- Hex Encoding

- Unicode 

- UTF - 8,16be,32

- Decimal Encoding