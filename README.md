# SOP - Same Origin Policy

__SOP__ is a policy that stops one website from reading or writing data to another website. (ensures basic web security).
- Allows a website to request data from its own URL and blocks anything from an external URL

* A security restriction on what web content JavaScript can interact with. JavaScript can manipulate the DOM object through DOM API provided by browser, JavaScript can also interact with server through browser built-in XMLHttpRequest object.

The policy checks for three different things in the origin :
1. Protocol
2. Host
3. Port

Only if these three are the same for two different origins, the browser allows cross origin read or write


# CORS - Cross Origin Resource Sharing

__CORS__ is a HTTP header based mechanism tha allows a website on one URL to request data from another URL

website 1 request data from website 2 of certain content type> only if access- control-allow-origin : website 1 and access-control-allow-header: content type desired> cors work

* Json csrf with adobe flash is possible(uses redirection)>victim to vulnerable.com >
        a content type header is added to the flash file and all the json data that we want to send and end point is also specified not vulnerable.com but 307 redirect page> which in turn redirects to vulnerable.com


- CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. 

- In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

## How CORS Work?

1. Client request

    When the browser is making a cross-origin request, the browser adds an Origin header with the current origin (scheme, host, and port).

2. Server response 

    On the server side, when a server sees this header, and wants to allow access, it needs to add an Access-Control-Allow-Origin header to the response specifying the requesting origin (or * to allow any origin.)

3. Browser recieves response

    When the browser sees this response with an appropriate Access-Control-Allow-Origin header, the browser allows the response data to be shared with the client site.


`app.use(cors({ origin: "http://localhost:1234" }))`

This code tells your app to send down the Access-Control-Allow-Origin header with the value of http://localhost:1234


- If Access-Control-Allow-Origin is set as * , it allows all url.


###  CORS divides cross-site requests into two categories.


1. Simple Request

  These are CORS requests that do not include credentials (cookies, HTTP authentication, etc.) in the request.

2. Preflighted Request
- Any HTTP request with non-standard headers like PUT PATCH, DELETE will need to go through __PreFlight__ (It's like a sanity check to ensure that it's safe)

- this kind of requests first send an HTTP request by the ‘__OPTIONS__’ method to the resource on the other domain, to determine if the actual request is safe to send.

- preflight request will contain the __Access-Control-Request-Method__ and __Access-Control-Request-Headers__ headers. These headers contain the value of the method and headers that the client wants to use in the request and the server will return back if the method and headers are valid.

`const cors = require("cors")`

`app.use(
  cors({
    origin: "http://localhost:1234",
    methods: ["GET", "POST", "PUT", "DELETE"],
    allowedHeaders: ["Content-Type"],
  }))`


  -  Potential for cross-domain attacks if a website's CORS policy is poorly configured and implemented. 

  - CORS is not a protection against cross-origin attacks such as __cross-site request forgery (CSRF)__.

  - __Access-Control-Allow-Credentials: true__ ,__ it allows request to contain cookies


  ### Testing for CORS Misconfiguration
  1. Change the origin header to an arbitrary value
    if `we add origin:https://example.com` we get `Access-Control-Allow-Origin:https://example.com as a response`

  2. change the origin header to a null value (use iframe)
  it means that the server is allowing requests from a specific situation where the origin cannot be determined or is considered untrusted.
    `origin : null` we get `Access-Control-Allow-Origin:null`

  3. changet the origin header to one that begins with the origin of the site
    if `url: https://portswigger.com`
    `origin: https://portswigger.com.scam.com`
    `Access-Control-Allow-Origin:https://portswigger.com.scam.com` is reflected in response.

4. change the origin header to one that ends with the origin of the site  (checking if it trusts all subdomains)

    `url: https://portswigger.com`
    `origin: https://randomscam.portswigger.com.`
    `Access-Control-Allow-Origin:https://randomscam.portswigger.com` is reflected in response.



## Browsers might send the value null in the Origin header in various unusual situations:


1. Cross-origin redirects.  
  When a web page makes a request to a different domain and that domain redirects the request back to the original domain, the Origin header may be set to "null."
2. Requests from serialized data.
 if a request is initiated from a piece of serialized data, like a Blob or a FormData object, and not from an actual web page (A Blob (Binary Large Object) is a data structure used in web development to represent large binary data, such as images, videos, audio, or any other type of raw data) (FormData is a built-in JavaScript object that allows you to create and manage data in HTML form format. It is typically used to send form data to a server via an XMLHttpRequest or a Fetch API request.)
3. Request using the file: protocol.
  When a web page is loaded from the local file system using the "file://" protocol and makes a cross-origin request
4. Sandboxed cross-origin requests.
  If a web page is loaded inside a sandboxed environment that restricts its access to certain features, the Origin header might be set to "null."



#### some XSS attacks can indeed be prevented through effective use of CSRF tokens.
By including CSRF tokens in requests that modify server state, the application can prevent attackers from exploiting the reflected XSS vulnerability to perform malicious actions. It ensures that even if the attacker successfully injects malicious scripts, the requests will be rejected by the server without the correct CSRF token.


### Intranets do have an impact on CORS policies, especially regarding requests without credentials.
 However, security practices and proper configuration are still essential to maintain a secure environment and prevent potential vulnerabilities.(Intranets are private networks within organizations that allow internal communication and collaboration.)


 ### How to prevent CORS-based attacks
 1. How to prevent CORS-based attacks
 2. Only Allow Trusted Sites:
 3. Avoid Whitelisting Null:
 4. Avoid Wildcards in Internal Networks: When * is used, any domain is allowed to access the resources
 5. CORS Is Not a Substitute for Server-Side Security:

*****
# CSRF - Cross-site request forgery

__Cookie Flow__
when you login in a web app > you get a cookie> which gets stored in your browser> whenever you make a request to this web application > the cookie in the browser is automatically send automatically - so the web app can verify your login.

__cross domian access control__

- In html we have iframe, an iframe allows you to embed one website in another website.
- But the cross iframe is not possible due to same origin policy (this is important because cookies are automatically sent on every request you make to that domain unless there are some flags set)


- The policy only allows you to read from the iframe if the conditions of same origin policy is satisfied.

 
`website URL: https://google.com:80`
`iframed URL: https"//google.com:80`

* (this not only applies to iframe but ajax request)(ajax request-  Asynchronous JavaScript and XML. It is a technique used in web development to make requests to a server and retrieve data without needing to reload the entire web page, update and loads only specific parts of the page.> XMLHttpRequest,fetch(), $.ajax())


## __Cross-site request forgery__
 when one domain is making one or more requests to another domain in order to modify some value.

one of the __solution__ is to use randomised tokens called __Anti CSRF Tokens__
* this will be generated randomly by the server side web application in the backend and sent to the website
* on every request it's validated (so another website can't make cross origin request or cross domain request)


## How CSRF Works

For a CSRF attack to be possible, three key conditions must be in place:

* A relevant action (like changing email id, password etc)
* Cookie-based session handling (when you login you get a cookie which is used for verifying identity)
* No unpredictable request parameters (attacker can't access or guess password, so it can't be requested)

## Common defences against CSRF

* CSRF tokens: The server validates the token before processing the request, ensuring that the request originates from the legitimate user session.
  - A common way to share CSRF tokens with the client is to include them as a hidden parameter in an HTML form

* SameSite cookies:
- SameSite is a browser attribute for cookies that controls when they are sent in cross-origin requests.
- By setting SameSite attributes to "Lax" or "Strict," cookies are restricted from being sent with cross-origin requests, thus reducing the chances of CSRF attacks. In "Lax" mode, cookies are not sent in requests triggered by external sites that navigate to your site. In "Strict" mode, cookies are not sent in any cross-origin requests.
* Referer-based validation: Some applications use the HTTP Referer header to validate whether the request originates from the same domain. - This approach is less secure than using CSRF tokens, as the Referer header can be spoofed or removed by certain browser privacy settings.




# Difference between XSS and CSRF

Cross-site scripting allows an attacker to execute arbitrary JavaScript within the browser of a victim user. 

Cross-site request forgery allows an attacker to induce a victim user to perform actions that they do not intend to. 



## flaws in CSRF token validation

1. Some application correctly validates the CSRF token when the request uses POST method but not when GET method is used.(so attacker might replace post with GET method to bypass validation)

2. Some application correctly validates the token when it is present but not if token is missing or removed.(so attacker can just remove the csrf token and attack)

3. CSRF token is not tied to the user session 
(checks if the csfr token is correct but doesn't check if the csrf belongs to the respective user. so attacker can use a completely different csrf token to attack a user since the token is not tied to the user's session cookie)


4. CSRF token is tied to a non-session cookie  `non-session cookie (csrfKey)`

- there are two cookies present beacuse the application uses different frameworks and has different function

- ( some application tie session cookie to csrf key, but sometimes csrf key and csrf token is not tied to the correct session cookie > so if we attach another csrf key and csrf token and try to manipulate > it would work)


- first check if csrf token is tied to csrf key > by submitting invalid crsf token > or > submit a valid csrf token of another user
- submit valid csrf token and csrf key of another user


5. CSRF token is simply duplicated in a cookie
(Double submit defense)

- both csrf token and csrf key is send to the server and only if both are same , the request is accepted

- This is used in stateless aplication, where application don't store any session state or session cookies in the backend (applications that use jaw token)  
- the value of csrf token and csrf key doesn't matter , it can be anything > but both have to be the same

