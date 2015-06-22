---
author: lime.postback.se
comments: true
date: 2014-07-08 12:29:50+00:00
layout: post
slug: securing-a-http-service-with-hmac
title: Securing a HTTP service with HMAC
wordpress_id: 153
categories:
- Game
- HTTP
- Javascript
- Security
- Uncategorised
---

**Intro:**

For a game i wanted to publish the highscore online. To be able to publish it online i needed a REST/HTTP service that could accept the highscore update. The service needed to be secure to make sure that only my game could update the highscore but i wanted to avoid forcing the game user to have to give a username and password to do this.

The straight forward approach would have been to use SSL at the web site and basic authentication with the same uid and pwd for every client. The problem though is that i do not have SSL support at my hosting provider....

So i needed to make this work in another way. I checked different solutions and the most common one seems to be to use HMAC(Hashbased Message Authentication Code) to secure the calls. Read more: [http://en.wikipedia.org/wiki/Hash-based_message_authentication_code](http://en.wikipedia.org/wiki/Hash-based_message_authentication_code).

The basic theory behind HMAC is to share a secret key to all devices/programs that are going to use your system. This key is used to hash the messages sent. The key is stored in a http request header field and validated on the server(that allso have the same key). There are some pitfalls with this solution, if you only hash the message with the key, you could have a "man in the middle" attack, where the message could be changed. To avoid this it's important to include as much as possible from the http request itself so it's possible to calculate a hash based on the content of the request. As an addition, it's good to have a timestamp in a header to avoid replay attacks.

I tested the solution in Javascript, and i noticed that there was a lot of the standard headers missing or not possible to set from Javascript. For instance was the MD5-Content header emtpy. This is a hash by itself of the message content. The MD5-Content and the secret key would be perfect input to the hash function.

MD5 example on server side: [http://www.piotrwalat.net/hmac-authentication-in-asp-net-web-api/](http://www.piotrwalat.net/hmac-authentication-in-asp-net-web-api/)

The algorithm for generating the HMAC key was SHA-1.

**Overall flow:**
Defined two new http headers:
- X-HMAC that holds the security hash
- X-Timestamp that holds the time stamp from when the request was sent
The client calculate the hash based on the security key and the X-Timestamp and sets that key in X-HMAC.
The serverside does the same calculation with the pre shared key and the X-Timestamp and compares that value with the value in the header X-HMAC.

**Details:**

Client side(Web(Javascript) together with Angular):

- I used [https://code.google.com/p/crypto-js/#HMAC](https://code.google.com/p/crypto-js/#HMAC) as the client crypto library
- Registered an interceptor in Angular.js for the http module to add the headers.
`
(function () {
'use strict';

angular.module('common')
.factory('hmacInterceptor', ['$log', hmacInterceptor]);

function hmacInterceptor($log) {

$log.debug('In interceptor');
var sessionInjection = {
request: function (config) {
var now = new Date();
var xTimestamp = now.toUTCString();
config.headers['X-Timestamp'] = xTimestamp;
config.headers['X-HMAC'] = CryptoJS.HmacSHA1(xTimestamp, "bybbe12345bybbe33333");
$log.debug(config.headers);
return config;
}
};

return sessionInjection;
}
})();
`


Server side(Web API/.NET):

- Nice built in crypto support
- Easy to extend the Authorization attribute to check the HMAC header instead

`
public class APIAuthorizationAttribute : AuthorizeAttribute
{

private string XHMAC = "X-HMAC";
private string XTIMESTAMP = "X-Timestamp";

private string SECRET;

public APIAuthorizationAttribute()
{
try
{
SECRET = System.Configuration.ConfigurationManager.AppSettings["HMACSecret"];
}
catch (Exception ex)
{
throw new Exception("Failed to read secret from web config", ex);
}
}

protected override bool IsAuthorized(System.Web.Http.Controllers.HttpActionContext actionContext)
{
var headers = actionContext.Request.Headers;
var clientHash = headers.GetValues(XHMAC).FirstOrDefault();

var serverHash = getHashFromMessage(actionContext.Request);

if (clientHash.Equals(serverHash, StringComparison.InvariantCultureIgnoreCase))
{
return true;
}
else
{
return false;
}

return true;
}

private string getHashFromMessage(System.Net.Http.HttpRequestMessage requestMessage)
{
var headers = requestMessage.Headers;
string timestamp = headers.GetValues(XTIMESTAMP).FirstOrDefault();
var clientHash = headers.GetValues(XHMAC).FirstOrDefault();

byte[] content = System.Text.Encoding.UTF8.GetBytes(timestamp);
byte[] secret = System.Text.Encoding.UTF8.GetBytes(SECRET);
HMACSHA1 hmac = new HMACSHA1(secret);
var serverHash = BitConverter.ToString(hmac.ComputeHash(content)).Replace("-", "");

return serverHash;
}
}
`
**Summary:**
In the end the solution got a little bit dirty, i would have liked to use the pre defined headers for MD5-Content, but it seems that it's not used anymore. Many people points against that it's not needed due to support in lower level protocol (TCP).



References:

- [http://www.piotrwalat.net/hmac-authentication-in-asp-net-web-api/](http://www.piotrwalat.net/hmac-authentication-in-asp-net-web-api/)
- [https://code.google.com/p/crypto-js/#HMAC](https://code.google.com/p/crypto-js/#HMAC)
- [https://docs.angularjs.org/api/ng/service/$http](https://docs.angularjs.org/api/ng/service/$http)
- [http://www.webdeveasy.com/interceptors-in-angularjs-and-useful-examples/](http://www.webdeveasy.com/interceptors-in-angularjs-and-useful-examples/)
- [http://www.breezejs.com/documentation/controlling-ajax](http://www.breezejs.com/documentation/controlling-ajax)
- [https://github.com/Breeze/breeze.js/blob/master/src/b00_breeze.ajax.jQuery.js](https://github.com/Breeze/breeze.js/blob/master/src/b00_breeze.ajax.jQuery.js)
- [http://en.wikipedia.org/wiki/Hash-based_message_authentication_code](http://en.wikipedia.org/wiki/Hash-based_message_authentication_code)
- [http://en.wikipedia.org/wiki/List_of_HTTP_header_fields](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields)
- [http://s3.amazonaws.com/doc/s3-developer-guide/RESTAuthentication.html](http://s3.amazonaws.com/doc/s3-developer-guide/RESTAuthentication.html)
