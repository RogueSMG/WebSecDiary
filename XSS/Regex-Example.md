```
^((?=.*[a-z]){2}(?=.*[A-Z]){2}(?=.*[\d]){2}).{6}$
```

- **?=** : matches a string followed by a specific substring defined before it  
- Start
- Anything followed by anything and 2 small letters
- Anything followed by anything and 2 caps letters
- Anything followed by anything and 2 digits
- Anything of 6 chars
- End