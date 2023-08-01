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



###  CORS divides cross-site requests into two categories.


1. Simple Request
- If Access-Control-Allow-Origin is set as * , it allows all url.


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