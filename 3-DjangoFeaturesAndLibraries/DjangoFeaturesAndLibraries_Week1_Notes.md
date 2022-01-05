##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #3: Django Features and Libraries

### Week 1 - Cookies and Sessions

#### Cookies and Sessions

Cookie Definition: HTTP cookies (also called web cookies, Internet cookies, browser cookies, or simply cookies) are small blocks of data created by a web server while a user is browsing a website and placed on the user's computer/device by the user’s web browser. Cookies are placed on the device used to access a website, and more than one cookie may be placed on a user’s device during a session.    

- Created by the server: The server creates some cookie data, and then sends it to the client browser
-	Stored on the client: The client browser stores the cookie data in the client device’s memory, and the client sends the cookie when it communicates with the server
- Cookie expiration: temporary or persistent    
    -	A temporary or session cookie (AKA in-memory cookie, transient cookie or non-persistent cookie) exists only in temporary memory while the user navigates a website.  Session cookies expire or are deleted when the user closes the web browser.  Session cookies are identified by the browser by the absence of an expiration date assigned to them. Session cookies should NOT be confused with sessions.
        -	Effectively a session cookie is a way of marking each browser, that the web server meets, with a big random number. Using Firefox, in developer tools,  you can find that cookie labeled as the “sessionid”. The sessionid is used to lookup the session database which is stored on the server.
    -	A persistent cookie, AKA tracking cookie, expires at a specific date or after a specific length of time. For the persistent cookie's lifespan set by its creator, its information will be transmitted to the server every time the user visits the website that it belongs to, or every time the user views a resource belonging to that website from another website (such as an advertisement).


-	To have the web server create a cookie on the web browser, use the HttpResponse.set_cookie() method:
    -	resp.set_cookie('zap', 42) # No expired date = until browser close
    -	resp.set_cookie('sakaicar', 42, max_age=1000) # seconds until expire
    -	Where “resp” is an HttpResponse object
      -	e.g.,
        resp = HttpResponse('In a view – some random text to be sent to the browser client')


-	request.COOKIE
    -	Every time a client browser makes a request to a server, it also attaches all its cookies associated with that web server, to the request. In the request object, the COOKIE attribute contains the key-value pairs of the cookie.
    -	HttpRequest.COOKIES is an attribute – it is a dictionary variable containing all the client browser’s cookies (associated with the web server to where the client HttpRequest is being sent). The keys and values in this dictionary variable are strings.


-	Here is an example of a views.py function that accesses the COOKIE attribute to specifically manipulate a cookie named ‘zap” and another cookie named ‘sakaicar”:

    ```
    from django.http import HttpResponse

    def cookie(request):
        print(request.COOKIES)
        oldval = request.COOKIES.get('zap', None)
        resp = HttpResponse('In a view - the zap cookie value is '+str(oldval))
        if oldval :
            resp.set_cookie('zap', int(oldval)+1) # No expired date
        else :
            resp.set_cookie('zap', 42) # No expired date = until browser close
        resp.set_cookie('sakaicar', 42, max_age=1000) # seconds until expire
        return resp
    ```    

      -	In this example, the session identifier cookie, ‘sessionid’, cookie would also be stored in the HttpResponse.COOKIE attribute.    



#### Django Sessions

Session Definition: an HTTP Session is a temporary and interactive information interchange between a browser client and a web server. An established communication session can involve multiple messages in each direction. A session is stateful – in this case, the web server holds current state information in a database about the session history to be able to communicate with the browser client (as opposed to stateless communication, where the communication consists of independent requests with responses).

- In most server applications, as soon as we start a session for a new (unmarked) browser we create a session.
-	The web server sets and sends a session cookie to be stored in the browser, which indicates the session id in use – this action gives this browser a unique “mark”.
    -	The creation and destruction of sessions is handled by a Django middleware that we use in our applications
-	The session identifier, sessionid, is a large, random number that the web server places in a browser cookie the first time we encounter a browser
    -	This number is used to pick from the many sessions that the server has active at any one time.


-	In Django, you have to enable the automatic setting of a sessionid cookie in the Dango project settings.py file, and specifically in the MIDDLEWARE list variable:

    ```
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
        'social_django.middleware.SocialAuthExceptionMiddleware',   # Add
    ]
    ```

-	Server software stores data in the session that it wants to have from one request to another from the same browser.
    -	HttpRequest.session is an attribute of the HttpRequest class. It is readable and writable, dictionary-like object that represents the current session.
    -	In Django, the HttpRequest session data is placed in a database stored on the web server.
        -	If you are using SQLite3, it is stored in a SQLite3 table that is part of your project model


-	Here is an example of a views.py function that accesses the HttpRequest.session attribute to access and manipulate a session key-value pair with a key named ‘num_visits’:
    ```
    def myview(request):
        num_visits = request.session.get('num_visits', 0) + 1
        # the above line creates the variable ‘num_visits’
        # with an initial value of 0, and then increments by 1
        # every time the page is loaded in a request
        request.session['num_visits'] = num_visits
        resp = HttpResponse('view count='+str(num_visits))
        resp.set_cookie('dj4e_cookie', 'b6d7a951', max_age=1000)
        return resp
    ```    

    -	It’s important to note that ‘num_visits’ is not a cookie nor is it the entire session – rather, it is one piece of the data that gets set in the client browser-web server session
    -	The Django web server uses the sessionid to locate and access the correct database instance which contains the relevant information for that specific browser-server session.
    -	In Django, we can find the session data in a table named ‘django_session’ that is part of our project model
        -	This table will have the following row structure:
            - the sessionid
            -	the data encoded in Base64, in our example num_visits is the only key-value pair in the data
                -	The data is organized in JSON format, which is helpful if there is a more complex set of data in the session
            -	the session expiration date-time


  ##### Postscript attributions:

  *The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

  *Initial Development: Charles Severance, University of Michigan School of Information*

  *Insert new Contributors and Translators here including names and dates:*  
  *Tim Castle, January 2022*
