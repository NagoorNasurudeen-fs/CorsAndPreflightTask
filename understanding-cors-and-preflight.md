# concepts

1. What is CORS and why it exists

CORS means Cross Origin Resource Sharing . It allows a web application from one origin to access the resources from other origins regardless of the Same-Origin policy(SOP) enforced by the browser. It exists to solve the problem that is if an origin is compromised by a malicious script, that script could communicate with another origin using the user's credentials. So the browser blocks reading of the response from the cross-origin by using SOP. But today most of the systems deploy frontend and backend servers separately.To allow the communication between the frontend and the backend and securely bypass the SOP, we need to setup CORS.

2. What is an Origin

An origin is defined using a combination of scheme + host + port . The scheme refers to the protocol used in the connection . eg HTTP ,HTTPS ,FTP etc. The host refers to the domain name eg `example.com` , `api.example.com`. The port refers to the port of the process. For same origin requests, scheme ,host and port must be same. If any of these is not same,then it will be treated as Cross-Origin request. 

3. What is a Cross-Origin Request

A request is treated as a cross origin request if it attempts to fetch or communicate a different origin. For example consider `https://example.com` as origin ,the same origin request will be in forms like `https://example.com/app/book` ,`https://example.com/stat/12` etc. The cross origin request will be in the form like `http://example.com` (different protocol),`https://api.example.com `(different domain),`https://example.com:4000` (different port).

4. Simple Requests vs Preflighted Requests

 A simple request is a request that is considered safe enough to send to the server without asking prior permission. 

 Criteria for Simple requests:
* it should use any of these following methods. Allowed methods = get,post,head 
* it should only include the standard headers ,automatically set headers.
* accepted content-type are application/x-www-form-urlencoded,multipart/form-data,text/plain.

A preflight request is the request that doesn't meet the criteria of simple request. These requests can modify the resource in the server or include custom headers. 

5. What is a Preflight OPTIONS Request

 For a preflighted requests, the browser first sends an OPTIONS request automatically before sending the actual  request. This request contains no body but headers such as origin , access-control-request-method,access-control-request-headers.The server responds with its CORS policy headers such as access-control-allow-origin ,access-control-allow-methods ,access-control-allow-headers.

6. What Triggers a Preflight

* If a request contains methods which are not simple http methods eg PUT,DELETE,PATCH etc. 

* If it contains headers which are not cors-safelisted headers. eg Authorization, X-Requested-With etc

* If the content-type is other than application/x-www-form-urlencoded,multipart/form-data,text/plain. eg text/xml,application/xml,application/json

7. Key CORS Response Headers

* access-control-allow-origin = It tells the browser which frontend origins are permitted to see the response of the request.
* access-control-allow-methods = It tells the browser about the allowed methods from the origin to access the resource.
* access-control-allow-headers = It tells the browser about the allowed headers from the origin.
* access-control-allow-credentials = It allows the frontend script to send and read credentials (like cookies) during a cross-origin request.

__syntax:__
```
access-control-allow-origin : http://example.com
access-control-allow-methods: get, post, put
access-control-allow-headers: content-type ,authorization
Access-Control-Allow-Credentials: true
```
we cannot set `Access-Control-Allow-Credentials: true` while simultaneously using a wildcard `Access-Control-Allow-Origin: *` . If we do so , then any malicious website could make authenticated requests on behalf of the user and read the responses if credentials and a wildcard origin were allowed together.

8. Why it works in Postman or curl but not in the browser

Since browser stores sensitive information like cookies and session data, it enforces SOP(Same origin policy). So the frontend needs to configure CORS to enable communication with the backend. But Postman and curl are standalone desktop applications and do not store any sensitive information , it does not need CORS setup.All the requests are performed on behalf of the developer.

# Real-World CORS Problem – Learning from a Developer

## Problem: 
A CORS error occurred in the production environment because the backend was configured with a wildcard origin (Access-Control-Allow-Origin: *). When a web application attempts to make cross-origin requests that include credentials (like cookies or authorization headers), browsers strictly block the request if a wildcard is used instead of a specific domain.

## Solution Taken:
 The wildcard configuration was removed, and the backend CORS settings were updated to explicitly list the specific, trusted domain origins allowed to make API calls.

## My Takeaway:
 I now understand that combining Access-Control-Allow-Origin: * with credential-based requests creates a security and functional conflict that browsers will reject. In future projects, I will avoid using wildcards in production and ensure that exact, authorized origins are explicitly mapped out from the start.