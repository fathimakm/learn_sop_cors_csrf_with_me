# SOP - Same Origin Policy

__SOP__ is a policy that stops one website from reading or writing data to another website. (ensures basic web security).
_ Allows a website to request data from its own URL and blocks anything from an external URL

* A security restriction on what web content JavaScript can interact with. JavaScript can manipulate the DOM object through DOM API provided by browser, JavaScript can also interact with server through browser built-in XMLHttpRequest object.

The policy checks for three different things in the origin :
1. Protocol
2. Host
3. Port

Only if these three are the same for two different origins, the browser allows cross origin read or write


# CORS - Cross Origin Resource Sharing

__CORS__ is a mechanism tha allows a website on one URL to request data from another URL