##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #4: Using JavaScript, JQuery, and JSON in Django

### Week 2 - Object Oriented JavaScript

#### JavaScript Object Oriented Concepts

-	Class - a template
-	Method - A defined capability of a class – AKA message
-	Attribute - A defined data item in a class – AKA fields, properties
-	Object - A particular instance of a class – AKA instance
-	State – state of an object, the set of values of an object’s attributes
-	In JavaScript, the `function` keyword returns a value which is the function itself


#### JavaScript Object Oriented Classes

-	In JavaScript, the `function` keyword is used to define a class    
      ```
      function ClassName(arg1, …, argN) {
          // Declare the class attributes
          this.attributeName0 = 0;
          // ”this” refers to the instance variable that is
          // constructing this class object
          this.attributeName1 = arg1;
          …
          this.attributeNameN = argN;
          …
          codeBlock;    

          // Define the class methods
          this.methodName = function () {
              codeBlock;
          }
      }
      ```    
      - Notice that the method definition invokes the keyword `function`, but unlike the use of `function` for the class name, this invocation of `function` is unnamed.
- For example:    
    ```
    function PartyAnimal(nam) {
        this.x = 0;
        this.name = nam;
        console.log("Built "+nam);
        this.party = function () {
            this.x = this.x + 1;
            console.log(nam+"="+this.x);
        }
    }
    s = new PartyAnimal("Sally");
    s.party();    

    j = new PartyAnimal("Jim");
    j.party();
    s.party();
    ```    
    -	the keyword `new` is used to create a new instance of a class – i.e., create an object


-	You may see JavaScript  code that has different syntax from the form and example above
    - Be aware that the code may have been written with ES6
    -	Among other things, JavaScript ES6 provides new syntax and features with the goal of making JavaScript code more modern and more readable





##### Postscript attributions:

*The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

*Initial Development: Charles Severance, University of Michigan School of Information*

*Insert new Contributors and Translators here including names and dates:*  
*Tim Castle, January 2022*
