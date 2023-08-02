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

__CORS__ is a mechanism tha allows a website on one URL to request data from another URL

website 1 request data from website 2 of certain content type> only if aceess- control-allow-origin : website 1 and access-control-allow-header: content type desired> cors work

* Json csrf with adobe flash is possible(uses redirection)>victim to vulnerable.com
        a content type header is added to the flash file and all the json data that we want to send and end point is also specified not vulnerable.com byrt 307 redirect page> which in turn redirects to vulnerable.com

-  HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources


- CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

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


  ### Testing for CORS Misconfiguration
  1. Change the origin header to an arbitrary value
    if `we add origin:https://example.com` we get `Access-Control-Allow-Origin:https://example.com as a response`

  2. change the origin header to a null value (use iframe)
    `origin : null` we get `Access-Control-Allow-Origin:null`

  3. changet he origin header to one that begins with the origin of the site
    if `url: https://portswigger.com`
    `origin: https://portswigger.com.scam.com`
    `Access-Control-Allow-Origin:https://portswigger.com.scam.com` is reflected in response.

4. change the origin header to one that ends with the origin of the site  (checking if it trusts all subdomains)

    `url: https://portswigger.com`
    `origin: https://randomscam.portswigger.com.`
    `Access-Control-Allow-Origin:https://randomscam.portswigger.com` is reflected in response.



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
* this will be generated randomly by the web application in the backend and sent to the website
* on every request it's validated (so another website can't make cross origin request or cross domain request)


