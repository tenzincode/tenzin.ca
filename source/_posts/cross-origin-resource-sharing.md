---
title: cross-origin resource sharing
date: 2019-03-14 12:21:08
tags:
  - javascript
  - cors
  - xhr
  - ajax
---

Cross-Origin Resource Sharing (CORS) is a mechanism that uses additional HTTP headers to tell a browser to let a web application running at one origin (domain) have permission to access resources from a server at a different origin. A web application executes a cross-origin HTTP request when it requests a resource that has a different origin (domain, protocol, and port) than its own origin.

XMLHttpRequest or Fetch invocations, web fonts, WebGL textures, and images/video frames drawn to a canvas using `drawImage()` use this cross-origin sharing standard to enable cross-site HTTP requests.

![Cross-Origin Resource Sharing](/images/cors.png)

## Access Control Scenarios

### Simple Requests

A request that does not trigger a CORS preflight. A simple request meets all of the following criteria:

- Method:

  - `GET`
  - `HEAD`
  - `POST`

- `Content-Type`:

  - `application/x-www-form-urlencoded`
  - `multipart/form-data`
  - `text/plain`

- CORS-safelisted request headers which can be manually set:

  - `Accept`
  - `Accept-Language`
  - `Content-Language`
  - `Content-Type`
  - `Downlink`
  - `Save-Data`
  - `Viewport-Width`
  - `Width`

- No event listeners are registered on any `XMLHttpRequestUpload` object used in the request

- No `ReadableStream` object is used in the request

### Preflighted Requests

These requests first send an HTTP request by the `OPTIONS` method to the resource on the other domain, in order to determine whether the actual request is safe to send. A request is preflighted if any of the following are true:

- Method:

  - `PUT`
  - `DELETE`
  - `CONNECT`
  - `OPTIONS`
  - `TRACE`
  - `PATCH`

- Non CORS-safelisted request headers

- `Content-Type` other than:

  - `application/x-www-form-urlencoded`
  - `multipart/form-data`
  - `text/plain`

- Event listeners are registered on any `XMLHttpRequestUpload` object used in the request

- `ReadableStream` object is used in the request

### Requests with Credentials

XMLHttpRequest and Fetch requests can also make "credentialed" requests, which are aware of HTTP cookies and HTTP Authentication information. By default, in XHR or fetch invocations, browsers will not send credentials. The `.withCredentials` property must be set to true.

`const invocation = new XMLHttpRequest();
const url = 'http://bar.other/resources/credentialed-content/';

```javascript
function callOtherDomain() {
  if (invocation) {
    invocation.open("GET", url, true);
    invocation.withCredentials = true;
    invocation.onreadystatechange = handler;
    invocation.send();
  }
}
```

Since this is a simple `GET` request, it is not preflighted, but the browser will reject any response that does not have the `Access-Control-Allow-Credentials: true` header.

Example exchange between client and server:

```
GET /resources/access-control-with-credentials/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/credential.html
Origin: http://foo.example
Cookie: pageAccess=2


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2.0.61 (Unix) PHP/4.4.7 mod_ssl/2.0.61 OpenSSL/0.9.7e mod_fastcgi/2.4.2 DAV/2 SVN/1.4.2
X-Powered-By: PHP/5.2.6
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain


[text/plain payload]
```

Because the request headers in the above response include a `Cookie` header, the request would fail if the value of the `Access-Control-Allow-Origin` header was '\*'.

If the request failed, the `Set-Cookie` in the above response would create an exception error.

Note that cookies set in CORS responses are subject to normal third-party cookie policies, and thus not be saved if the user has configured their browser to reject them.

## HTTP Request Headers

The following headers are used by clients to make cross-origin HTTP requests.

### Access-Control-Request-Method

The `Access-Control-Request-Method` is used when issuing a preflight request to let the server know what HTTP method will be used when the actual request is made.

```
Access-Control-Request-Method: <method>
```

### Access-Control-Request-Headers

The `Access-Control-Request-Headers` header is used when issuing a preflight request to let the server know what HTTP headers will be used when the actual request is made.

```
Access-Control-Request-Headers: <field-name>[,<field-name>]*
```

## Compatibility

- Internet Explorer 8 and 9 expose CORS via the `XDomainRequest` object, but have a full implementation in IE 10.
- While Firefox 3.5 introduced support for cross-site `XMLHttpRequests` and Web Fonts, certain requests were limited until later versions. Specifically, Firefox 7 introduced the ability for cross-site HTTP requests for WebGL Textures, and Firefox 9 added support for Images drawn on a canvas using `drawImage()`.
