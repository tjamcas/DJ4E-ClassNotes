##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #2: Building Web Applications in Django

### Week 4 - Forms in HTTP and HTML

#### Forms, GET, POST, and HTTP

-	Forms are the way we write HTML code to present fill-in-the-blank areas to a browser. There are two ways in which a browser client can send form data to a web server: `GET` and `POST`.
    -	`GET` requests: An anchor tag makes a `GET` request. In that anchor tag we can append a question mark and key-value parameters pair.
    -	A form can also use a `GET` request, or perform a `POST` request, or do both.
    -	By default, a form is sent to web server as a `GET` request. You must specify in your HTML form tag to send the data as a `POST` request
        -	`<form method="POST"> … </form>`


-	*`dumpdata` is utility code that returns the information in a `GET` or `POST` request. This information will take the form of dictionary key-value pairs. This utility code was written especially for the DJ4E course to illustrate the data generated between client browsers and web servers.*
    - *It resides in the `views.py` file at `dj4e-samples/getpost/views.py`.*
        ```
        # Call as dumpdata('GET', request.GET)

        def dumpdata(place, data) :
            retval = ""
            if len(data) > 0 :
                retval += '<p>Incoming '+place+' data:<br/>\n'
                for key, value in data.items():
                    retval += html.escape(key) + '=' + html.escape(value) + '</br>\n'
                retval += '</p>\n'
            return retval
        ```
        

- If a form uses `GET` requests, then it gets its data through the `request.GET` method.
    -	Recall from Django documentation: `HttpRequest.GET` is a dictionary-like object containing all given HTTP `GET` parameters.
      
        ```
        def getform(request):
            response = """<p>Impossible GET guessing game...</p>
                <form>
                <p><label for="guess">Input Guess</label>
                <input type="text" name="guess" size="40" id="guess"/></p>
                <input type="submit"/>
                </form>"""

            response += dumpdata('GET', request.GET)
            return HttpResponse(response)
        ```
      
-	If a form uses `POST` requests, then it gets its data through the `request.POST` object.
    -	Recall from Django documentation: `HttpRequest.POST` is a dictionary-like object containing all given HTTP POST parameters, providing that the request contains form data.
        
        ```
        @csrf_exempt
        def postform(request):
            response = """<p>Impossible POST guessing game...</p>
                <form method="POST">
                <p><label for="guess">Input Guess</label>
                <input type="text" name="guess" size="40" id="guess"/></p>
                <input type="submit"/>
                </form>"""

            response += dumpdata('POST', request.POST)
            return HttpResponse(response)
        ```
        
    -	A `GET` form will send a `GET` request to the web server, and append a question mark ("`?`") followed by the key-value parameters to the requested page.
        -	For example: `GET /form/getform?guess=42`
    -	A `POST` form will send a group of headers, a blank line, and then the parameters (in the shape of key-value pairs) in the `POST` request.


    -	**Two development rules for when to use GET vs. POST:**
        1. **Use POST when you create or modify data.**
        2. **Use GET for reading data.**

#### Forms and Cross Site Request Forgery (CSRF)

-	Cross-Site Request Forgery (CSRF):
    -	A rogue site "tricks" a user into completing and sending a form to a legitimate site that they are currently logged into. The form will automatically send the user's current cookie data which includes the session ID #. The rogue site does not see or know the cookie data, but the form can use the legitimate session ID # from the cookie data to then do something "dangerous" like change a database value or transfer funds. The legitimate site accepts and performs the request because the form has valid cookie session ID data.
    -	To combat CSRF exploits, we generate another large random number, the CSRF token, and include it in the session. When a form is sent that's coming from our server, we add an input hidden tag that has this CSRF token. When the POST request is received, the server check the CSRF token that's coming in the POST data against the one that's in the session. The request is rejected it if it doesn't match.
    -	Django already includes the CSRF protection middleware for the developer to include in their views. It is called in “settings.py” in the “MIDDLEWARE’ list variable. You MUST properly implement CSRF in your HTML form code by inserting a CSRF token as a hidden input. The next section will explain how to do this.

#### CSRF Support in Django

-	If you want to disable Django's CSRF protection which is enabled by default, insert "@csrfexempt" line into the views.py file directly above the specific view for which you do not want CSRF protection.
-	If CSRF is implemented incorrectly, or the CSRF token does not validate, then the browser client will receive a 403 (Forbidden) error - meaning that the CSRF verification failed, and the request has been aborted.
-	Here is the long form of code to implement CSRF protection (i.e., manual implementation). It resides in the application's views.py file:
    
    ```
    from django.middleware.csrf import get_token

    def csrfform(request):
        response = """<p>CSRF Success guessing game...</p>
            <form method="POST">
            <p><label for="guess">Input Guess</label>
            <input type="text" name="guess" size="40" id="guess"/></p>
            <input type="hidden" name="csrfmiddlewaretoken"
                value="__token__"/>
            <input type="submit"/>
            </form>"""

        token = get_token(request)
        response = response.replace('__token__', html.escape(token))
        response += dumpdata('POST', request.POST)
        return HttpResponse(response)
    ```

-	Here is the short form of code, using functions, to implement CSRF protection. This code resides in one of the application's form templates. Recall these would reside in the folder, for example, "/appname/templates/appname/guess.html".
    -	Since we’re creating a POST form (which can have the effect of modifying data), we need to worry about Cross Site Request Forgeries. Thankfully, you don’t have to worry too hard, because Django comes with a helpful system for protecting against it. In short, all POST forms that are targeted at internal URLs should use the `{% csrf_token %}` template tag.
    -	`{% csrf_token %}` will implement the CSRF protection by inserting the CSRF token as a hidden input.
        
        ```
        <p>Guessing game</p>
        {% if message %}
        <p>{{ message }}</p>
        {% endif %}
        <form method="post">
        <p><label for="guess">Input Guess</label>
        {% csrf_token %}
        <input type="text" name="guess" size="40" id="guess"/></p>
        <input type="submit"/>
        </form>
        ```
        
  -	This template would be called by a defined view in the application's `views.py` file:
    
    ```
    def guess(request):
        guess = request.POST.get('guess')
        msg = checkguess(guess)
        return render(request, 'getpost/guess.html', {'message' : msg })
    ```
    
  -	In this example, the checkguess function, which also resides in views.py, is called by the guess view. Here is the checkguess function:
    
    ```
    # Call as checkguess('42')
    def checkguess(guess) :
        msg = False
        if guess :
            try:
                if int(guess) < 42 :
                    msg = 'Guess too low'
                elif int(guess) > 42 :
                    msg = 'Guess too high'
                else:
                    msg = 'Congratulations!'
            except:
                msg = 'Bad format for guess:' + html.escape(guess)
        return msg
    ```
    
-	Rather than use function based views, we can write a cleaner code with class based views that have methods that Django automatically recognizes:
    
    ```
    class ClassyView(View) :
        def get(self, request):
            return render(request, 'getpost/guess.html')

        def post(self, request):
            guess = request.POST.get('guess')
            msg = checkguess(guess)
            return render(request, 'getpost/guess.html', {'message' : msg })
    ```
    

#### The POST Refresh Pattern

-	Problem description:
    -	Web app developer creates a form which POSTs data to a web server.
    -	User successfully uses form to POST data which intentionally causes the web server to perform some action, e.g., transfer money from one account to someone else.
    -	The form successfully executes its POST and the transaction completes, and potentially a success message is returned to the user.
    -	Problem scenario: if the user re-loads the page, then the form will POST again and the action will be repeated, e.g., the transfer of money will happen again. This is probably not what the user wants to do, and the browser will automatically send a cryptic message pop-up about whether the user really wants to repeat this action.
        -	The message is left to the browser, and will likely be confusing to the user.
        -	Therefore, the developer must take steps to control the user interaction in this scenario.


#### Implementing POST Redirect in Django

-	POST problem solution:
    -	Pattern rule - Never send HTML to the user browser at the end of a POST request. Instead return a redirect -- potentially to the very same page we generate.
    -	More specifically:
        1. User completes form and submits a POST
        2. Web server performs the transaction
        3. Web server sends a 302 redirect - potentially to the newly created page.
        4. The user's browser responds to the 302 message with a new GET request to the same page.
    -	However, we have to handle any messages -- e.g., a transaction success message to the user.


-	Flash Message technique:
    -	The POST code generates a transaction success (or failure?) message. The message is stored in a session parameter, and the session parameter is used in the GET request code.


-	We'll show a more complex way to do Flash Messages with Django code/libraries.
    -	In the application's views.py:
        
        ```
        class AwesomeView(View) :
            def get(self, request):
                msg = request.session.get('msg', False)
                if ( msg ) : del(request.session['msg'])
                return render(request, 'getpost/guess.html', {'message' : msg })

            def post(self, request):
                guess = request.POST.get('guess')
                msg = checkguess(guess)
                request.session['msg'] = msg
                return redirect(request.path)
        ```
        
    -	Effectively the above code does the following:
        1. We go to the page, and perform a GET request
        2. At this point the user has not provided any input, so there is no message
        3. The user inputs data - in this case, a guess -- and submits a post
        4. The web server processes the POST request:
              -	the guess is placed into a session variable named 'msg'
              -	the web server sends a 302 redirect to the current page
        5. The user's browser performs a GET to the current page
              -	The session variable 'msg' is deleted
              -	But the local variable, msg, is rendered in the return statement


  ##### Postscript attributions:

*The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

*Initial Development: Charles Severance, University of Michigan School of Information*

*Insert new Contributors and Translators here including names and dates:*  
*Tim Castle, January 2022*
