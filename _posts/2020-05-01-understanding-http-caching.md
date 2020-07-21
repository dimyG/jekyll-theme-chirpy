---
title: Understanding HTTP Caching
date: 2020-05-01 11:30:00 +0200
categories: [Web-Development, Django]
tags: [django, http]
toc: true
---

# Server caches and Downstream caches
HTTP caching mechanisms can be a bit confusing at first so let's start clarifying them a bit. I would like to start by noting 
that in a typical client web server architecture there isn't only one cache, there are many. There are server side caches and downstream caches. 
A server side cache can be a key-value store like redis or memcached for example. Apart from that there are also the so called downstream 
caches that store the response after it has left "the server". The most obvious downstream cache is the browser's cache. 
Have in mind though that there can be additional downstream caches between the server and the browser, 
for example your ISP's cache.  

In any case, you have the power to control the behaviour of these caches or in other words when and how a response is stored 
and retrieved from them, using HTTP headers. Headers like `Cache-Control`, `Expires`, `ETag`, `Last-Modified` and `Vary`. 
Let's dig into them...

# Cache-Control max-age and Expires
`Cache-Control` has many directives one of which is the `max-age` with which you can define for how many seconds after the first request, you
want the response to be stored in a cache. If a request for the same resource is done within this time frame then the cached response will be used no matter what.
`Expires` on the other hand does the same thing, with the difference that you define a specific date and time until which the cached response is valid. 
This has the disadvantage that if there are a lot of clients with the same cached data and the same Expires header, all these responses 
will expire at the same time and all clients might request the new resource at the same time overloading your servers. In any case 
the `Cache-Control` header is newer, more versatile and takes precedence over `Expires` if both are present. So the only reason you might want
to still use the Expires header is for outdated downstream caches which might not support the `Cache-Control` header yet.  

# ETag and Last-Modified
At first the browser checks the `Cache-Control` or `Expires` headers to determine whether or not to make a request to the server. 
If it has to make a request, then there are some additional headers that control what happens next. Meet `Etag` and `Last-Modified` response headers.
`ETag` is usually a checksum of the response which means that it changes whenever the response changes while `Last-Modified` 
is a datetime which declares when the response was last modified. These values are both calculated by your server. If an `ETag` 
header is present in a response, for example `ETag: "value_01"` and the browser makes a request for that resource, then 
it will add the request header `If-None-Match: "value_01"`. The server will calculate the ETag value for that response and if it 
matches the one that came with the request, it will respond with a status code 304 (Not Modified) and an empty body. So the cached value will be used.
If the response has changed, then the ETag value calculated by the server will be different and a new response will be created and send to the client. 
`Last-Modified` works in a similar way but instead of calculating and comparing a checksum value it calculates and compares a datetime value. 
The browser sends an `If-Modified-Since: <date and time>` request header. You can choose to use whichever of the two best suits you.
 
# ETag example
- Browser_A makes a GET request for page_1
- Page_1 doesn’t exist in browser’s_A cache. The request reaches the server. It doesn’t exist in the server cache. It is calculated. The response is added in the server cache along with its ETag value written as a response header. The response reaches the browser, is stored in its own cache and is presented to the user. 
- Browser_B requests the same page_1. It is not in its cache. The request reaches the server but the response is not calculated again since it exists in the server cache from where is retrieved and returned to the browser.
- Browser_A requests the page_1 again. Since there is no Expires or Cache-Control max age headers, only an ETag, it doesn’t get the page from ts cache but sends a request to the server along with its cached ETag value. The server calculates the current ETag value for that page. If it hasn’t changed then it responds with a simple 304 not modified response. The response reaches the browser which serves the user with its cached page.
- The database is updated which means that page_1 changes and a new ETag value must be generated for it. Browser_A requests page_1. The server calculates the ETag value and sees that it doesn’t match with the request's ETag value. It runs its business logic to calculate the new response and sends it to the browser. The browser serves it to the user and updates its cache with the new page_1 that has a new ETag header

# Caching variable and private data
Caching is an excellent efficiency boost, but there’s a danger to it. Cached data is stored as key-value pairs and many 
systems create the key for a web page from its url. The problem is that the same url might return different content 
based on which user sees the page or based on any other variable like if the client is a mobile device or not. 
Extra care must be taken for cases like this and the solution is the use of the `Vary` header. Using this header 
you can vary a response based on any request header. For example, if you define `Vary: User-Agent` then the 
caching servers should consider the user agent when deciding whether to serve the page from cache or not. If the `User-Agent` 
request header is the same with the one that was used when the page was first cached, then the page will be retrieved 
from the cache. Otherwise a request to the server will be made, a new response will be received and will overwrite 
the current cached version.

In other scenarios there are pages that contain private information which you don't want to save on any public cache 
like the ISP's cache for example, but only in private caches like the client's browser cache. For these cases you can
use the versatile Cache-Control header with the private directive, `Cache-Control: private`. For very sensitive data 
like credit card details that you don't want to be stored at all you can use `Cache-Control: no-store`.

# Caching in django in a nutshell
I will probably create a whole new post for this subject but until then, I live this overview of django caching here...

As we said, the cached responses might be stored in more than one caches, for example in the server's cache and in the 
browser's cache. Django offers you the proper tools that make it very easy to control the server cache and also 
to define the browser's cache behaviour using HTTP headers.

Before deciding which data should be cached, you first have to select where the data is going to be stored or in other words 
what server cache you will use. Django supports many options like a database, the filesystem, the server's memory or even 
better, external services like Memcached or Redis. Your cache preference goes in the `CACHES` 
setting in your settings file. After setting up where the cached data is stored you can move on to defining which 
data should be cached.

Django gives you full power over caching. First of all it offers different levels of cache granularity: 
1. You can cache your entire site
2. You can cache specific views
3. You can cache only the pieces that are difficult to produce

## Caching the entire site
The simplest way to use caching is to cache your entire site. Basically this is achieved with the use of middleware. 
The `FetchFromCacheMiddleware` that caches GET and HEAD responses with status 200, where the request and response headers allow 
and the `UpdateCacheMiddleware` that automatically sets the Expires and Cache-Control (with max-age directive) headers 
in each response. You just need to add these middleware to your MIDDLEWARE setting in the django settings file 
and define the default cache options. 

> ***Note***: You can disable caching for a view using the `@never_cache()` decorator. This decorator adds a 
"Cache-Control: max-age=0, no-cache, no-store, must-revalidate, private" header to a response to indicate that a page 
should never be cached. You can also easily patch the response's 
Cache-Control header using the `@cache_control()` decorator. For example if you don't want to use the default 
expiry time for a view, you can easily change it by decorating it with: @cache_control(max-age=60*15) or you can instruct public caches 
to not store a view with: @cache_control(private=True)

Cached data is stored in **key-value pairs**, so in order to understand when a cached value is accessed or not, we must 
understand how these keys are created. By default, Django’s cache system creates its cache keys using the requested fully-qualified URL, for example
https://www.example.com/stories/2005/?order_by=author. If the key which is created by this url exists in the cache, 
then any request for this url will receive the cached value.  

You can change the way the keys are being created using the Vary header. The Vary header defines which request 
headers a cache mechanism should take into account when building its cache key. The Vary header is controlled in django 
by the the `@vary_on_headers` view decorator. For example if we want to cache a different version of a page for each combination 
of User-Agent and Accept-Encoding Headers we can simply do:  
```python
@vary_on_headers('User-Agent, Accept-Encoding')
def my_view(request):
    ...
``` 
> ***Note***: When you use cookie based session authentication (where the session id is stored in a cookie) and the same 
>url returns different content based on which user requested the page, you need to vary the cached page by cookie. 
>Luckily, in this case (where the session middleware is used and session is accessed in a view), 
>django will automatically add `Vary:Cookie` header to the response so you don't have to do it yourself.

## Caching specific views
In the previous approach we cache the entire site by default and externally specify what we don't want to cache (or we want to cache differently). 
This is not always the best approach since you might end up caching things that shouldn't be cached. 
Another approach is to cache nothing by default and externally define what pages you want to cache and how. This is achieved 
with the `@cache_page` decorator.
```python
@cache_page(60 * 15)
def my_view(request):
    ...
```
> ***Note***: The per-view cache, like the per-site cache, is keyed off of the URL.

## Caching specific pieces
Sometimes, caching an entire page like the per-site or per-view cache strategies do, is not the best approach. 
For example you might have a view whose results depend on several expensive queries, the results of which change at different intervals. 
In this case you don't want to cache the entire page but you still want to cache those expensive results. To do so you 
can use django's low level cache API. Using it you can cache any Python object that can be [pickled](https://docs.python.org/3/library/pickle.html#module-pickle) 
safely, like strings, dictionaries, lists of model objects. 
You first access your configured caches defined in the CACHES setting through django.core.cache.caches 
and then externally store and retrieve things from it. 
```python
from django.core.cache import caches
def my_view(request):
    my_cache = caches['default']
    my_cache.set('my_key', my_value, 60 * 15)
    my_cache.get('my_key')
    ...
```




