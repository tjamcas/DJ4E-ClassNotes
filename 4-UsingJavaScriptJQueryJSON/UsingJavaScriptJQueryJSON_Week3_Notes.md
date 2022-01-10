##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #4: Using JavaScript, JQuery, and JSON in Django

### Week 3 - JQuery

#### JQuery and the Document Object Model

In an HTTP Response, the web server sends HTML and JavaScript code to the client web browser. The web browser parses the response in order to create the Document Object Model. It presents the HTML and executes the JavaScript code.
-	In the browser developer tools, if you view source, then you will see the HTTP Response. In developer tools, if you inspect element, then you will see the Document Object Model (DOM).
-	JavaScript can read and change the DOM, which changes what you are viewing through the screen presented by your web browser client.
-	JavaScript can intercept and interpret events, like onscreen clicks and hovers, and perform functions that will modify the DOM. These functions can be performed at the client and, once they reside at the client, be completely independent of the web server.
-	A current trend in web applications is to write more functionality using JavaScript and less in Django or on the backend server. In this type of hybrid web application, JavaScript talks to the database models directly and modifies the DOM – as opposed to going through the entire request-response cycle to produce a view informed by the model and forms.

The DOM is referenced as the “document” and, as you would expect from an object ,has attributes and methods. The DOM is a hierarchical set of trees and tags. The concept of ID fields was defined so that the DOM tree structure could be more easily navigated. An ID field can be placed on a tag and then used to access that tag’s contents.
-	document.getElementById('idName')
    -	this method gets the tag element that has an `id=”idName”`
-	document.getElementById('idName').innerHTML
    -	innerHTML is the text string between the tags of the element associated with id=”idName”
    -	Example:
        ```
        <p>
        Hello <b><span id="person">Chuck</span></b> there.
        </p>
        <script type="text/javascript">
        console.dir(document.getElementById('person'));
        var st = document.getElementById('person').innerHTML;
        console.log("ST = "+st);
        alert('Hi there ' + st);
        document.getElementById('person').innerHTML = 'Joseph';
        </script>
        ```
        -	`var st = document.getElementById('person').innerHTML;`  
        At this point, st is a local string variable with a value of “Chuck”
        -	`document.getElementById('person').innerHTML = 'Joseph';`    
        This changes the DOM, and the browser client will now display “Joseph” instead of “Chuck”.
-	JavaScript can be used to add elements into the DOM
    -	Example:
        ```
        <p>
        <a href="#" onclick="add();return false;">More</a>
        </p>
        <ul id="the-list">
        <li>First Item</li>
        </ul>
        <script>
        counter = 1;
        console.log(document.getElementById('the-list'))
        function add() {
            var x = document.createElement('li');
            x.className = "list-item";
            x.innerHTML = "The counter is "+counter;
            document.getElementById('the-list').appendChild(x);
            counter++;
        }
        </script>
        ```
        -	The JavaScript code incrementally adds an `li` list item to the `ul` unordered list when the “More” anchor tag is clicked.


#### Using JQuery

JQuery is a JavaScript library that interacts with the Document Object Model (DOM). As a library, it handles the unique requirements of each browser client so that JavaScript code is portable across most browser versions.

-	Documentation for JQuery can be found at jquery.com or StackOverflow.
-	When searching, be sure to specify “How do I (fill in blank) in jQuery?” or  How do I (fill in blank) in vanilla JavaScript?”
    -	Vanilla JavaScript refers to JavaScript that does not use JQuery
-	Pattern for using JQuery in HTML and JavaScript webpage:
    1.	Reference the JQuery library inside your HTML file using a script tag:
        ```
        <script type="text/javascript" src="jquery.min.js">
        </script>
        ```
            -	Although this could be loaded in the <head>, it is best practice to load it at the end of the <body> element so that your web page loads faster by downloading the called libraries last.

    2.	Create a second script tag and invoke the JQuery library with the “$” function
      ```
      <script type="text/javascript">
      $(elementName).eventName(function(){
        …
        // Insert JavaScript and JQuery code inside braces {…}
        …
      });
      </script>
      ```
              -	$ is the name of the jQuery function – remember that JavaScript functions can begin with “$”.    


-	Example #1:
    ```
    <script type="text/javascript">
    $(document).ready(function(){
      alert("Hello jQuery World!");
      console.log('Hello jQuery..');
    });
    </script>
    ```
    -	In this example, when the webpage is fully loaded (i.e., ready), an alert will pop to the screen and a message sent to the console
    -	`$(document)` is a JavaScript function that calls JQuery and loads the DOM ( document ) as an argument – remember that JavaScript implements first class functions where  function arguments can be another function. Effectively, `$(document)` returns a JQuery-wrapped instance of the DOM.
    -	`$(document).ready()` is a JQuery method that will run the code inside its parentheses once the page DOM is loaded and ready to execute JavaScript code. `ready()` is documented in the JQuery library at jquery.com and is a function with one argument. The single argument will be a function that the developer defines.
    -	`$(document).ready(function(){ … })` uses the full pattern for calling JQuery where the JavaScript function, `function(){ … }`, is invoked
    -	In summary: The `$` function is called and passes document as a single parameter. This returns an object which calls the ready method which passes one parameter, which is code in the form of an unnamed JavaScript function.


-	Example #2:
    ```
    <html
    <head>
    </head>
    <body>
    <script type="text/javascript" src="jquery.min.js">
    </script>
    <script type="text/javascript">
    $(window).resize(function() {
      console.log('.resize() called. width='+
        $(window).width()+' height='+$(window).height());
    });
    </script>
    <p>Here is some awesome page content</p>
    </body>
    ```
    -	In this example, when the browser window is resized, a message will be logged to the console that states the present window width and height.
    -	`resize()` is a JQuery method documented in the JQuery library at jquery.com, and it is a function with one argument.


-	Example 3:
    ```
    <p id="para">Where is the spinner?
      <img id="spinner" src="spinner.gif" height="25"
          style="vertical-align: middle; display:none;">
    </p><p>
    <a href="#" id="tog">Toggle</a> |
    <a href="#" id="red">Red</a>
    </p>
    <script type="text/javascript">
    $(document).ready(function() {
      $("#tog").on('click', function() {
        $('#spinner').toggle();
      });
      $("#red").on('click', function() {
        $('#para').css('background-color', 'green');
      });
    });
    </script>
    ```
    -	This is a common pattern using JQuery:
        -	Load the HTML, and use class or id attributes for targeted elements
        -	JQuery code runs after the document is ready and is applied to the targeted element
        -	`toggle()` is from the JQuery library
        -	`on()` is also a JQuery function for handling one or more events


-	Example #4:
    ```
    <p>
    <a href="#" id="the-href">More</a>
    </p>
    <ul id="the-list">
    <li>First Item</li>
    </ul>
    <script type="text/javascript"
           src="jquery.min.js"> </script>
    <script>
    counter = 1;
    $(document).ready(function(){
      $("#the-href").on('click', function() {
          console.log('Clicked');
          $('#the-list').append('<li>The counter is '+counter+'</li>');
          counter++;
      });
    });
    </script>
    ```
    -	`append()` is a JQuery function that inserts the content specified in its parameter to a matched element (elements with the matching class or id attribute)








##### Postscript attributions:

*The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

*Initial Development: Charles Severance, University of Michigan School of Information*

*Insert new Contributors and Translators here including names and dates:*  
*Tim Castle, January 2022*
