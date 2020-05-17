Ouestions:

- var x = 5 ;

  VS

  var x;

  x = 5;

- Js uses Unicode Charset

- vars are Identifiers

- var x; // Declaration      x = 5; // Assignment

- JavaScript objects are containers for **named values** called  properties or methods.

- Object Properties can be accessed by :

  - *objectName.propertyName*   //  person.lastname;
  - *objectName["propertyName"]*      //  person["lastname"];

- Objects can also contain Methods as Properties:

  - var person = {
       firstName : "John", lastName : "Doe", id : 5566, 

       fullName : function() { return this.firstName + " " + this.lastName; }
     };

- **this** keyword refers to the owner of the function (Eg. **this** refers to person in the above example)

- Any variable defined using the **new** keyword defines it as an object

- \ used as an escape character

- \+ (String Addition)can be used to break lines if they get too big

- **search() and indexOf()** does the same thing but the former cannot take a 2nd start posi param wheres the latter cannot handle regex searches

- JavaScript Numbers are Always 64-bit Floating Point

- **NaN** - Not a Number     // isNan(x) to check if number or not      // typeof NaN is Number  

- Num/Numeric-String becomes a Number. (Eg. 100/"10" --> 10)

- Hexadecimal is **base 16**. Decimal is **base 10**.  Octal is **base 8**. Binary is **base 2**.

- **Array is a Special type of Object**

- You can have objects in an Array. You can have functions in an Array. You can  have arrays in an Array:

- Array.forEach() calls a function for each array element.

- Arrays with named indexes are called associative arrays (or hashes). [Objects in Js]

- But in Js, **arrays** always use **numbered indexes**

- **"x" instanceof Array;**  or **Array.isArray(fruits);** to check if a variable is an Array. Also you can define a custom function

- \` can be used in place of paranthesis() in Js Functions - **Template Strings or Template Literals**