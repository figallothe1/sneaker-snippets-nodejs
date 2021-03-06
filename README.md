# Sneaker Snippets
Barebones Javascript snippets for people engaging in seeing the base code behind standard sneaker-development practices. A fundamental understanding of NodeJS/Javascript/Programming is required to understand some terminology and practises I may be using.

### Contents

The code snippets are placed in order from most beginning friendly to the most advanced techniques.

1) [My First Request](/snippets/requests) - A basic HTTP request to extract the page title from a website
2) [My First Monitor](/snippets/requests) - A basic monitor using HTTP request to extract the page title from a website and look for a change.
3) [My First Webhook](/snippets/requests) - A basic Discord webhook being sent using Discord.js


### Terminology

Many readers may not be familiar with some of the terminology. Here is a guide for anything that may confuse you.

1) [Status/Response Codes](#status-codes)
2) [Request Methods](#request-methods)
3) [Headers](#headers)
4) [Content Types](#content-types)
5) [Cookies](#cookies)
6) [Programmable Browsers](#programmable-browsers)
7) [Cache](#cache)
8) [Proxies](#proxies)


#### Status Codes

Status codes are numbers returned by a website to describe the type of action or data you are going to see. Universal status codes you will deal with are:

* 200 - The response has been successful
* 3XX - The response is redirecting you to another webpage; usually, 3XX response codes have no content in them and just a `location` header of the page you are about to be redirected to.
* 401 - Unauthorized. You are not authorized to access the page.
* 403 - Access denied, usually this means you've been banned or restricted on a website.
* 404 - This page cannot be found
* 407 - Tunnelling socket error, often shown when your program cannot connect to a proxy.
* 5XX - Server errors

Not receiving a status code is usually because something more internal has happened. If you try making an HTTP request without being connected to the internet, you will see an example of this. Theoretically, a server could provide a valid response as an invalid status code to try and trick you; this is unlikely as it would also create a challenging development experience.


#### Request Methods

Request methods define the way you plan to send information to a website. There are many different methods, but the most common two you will encounter are:

* GET - A `GET` request can only send information to a website via query parameters. Often used to get information rather than send it (hence the name)
* POST - A `POST` request is used to send information to a web server, this information can consist of many different formats. Please refer to [Content Types](#content-types) to read information about them.


#### Headers

Headers are snippets of key:value information sent to the browser to determine various types of information, most commonly which browser a user is using. The most well known anti-antibot technique is setting a user-agent header for a version of a real browser such as: `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36`. Matching all the headers with a browser also increases the anonymity of your request, the user-agent header isn't the only way browsers can determine which browser you're using.

The most common types of request headers you will see used are:
1) User-Agent - A string consisting of the browser/operating system and version.
2) Accept-Language - The server can use this to determine your language automatically.
3) Accept-Encoding - Used to define what type of encoding a server is allowed to send to you. It usually used to make the page file size smaller, potentially useful when using residential proxies with a limited amount of data. Most commonly `gzip`, `deflate` and `br`
4) Host - The host domain name. This parameter can be used in some good security bypassing techniques.
5) Cache-Control - This is used in determining the cache of the webpage. Usually, we don't want to see cached bypasses because they show out-of-date information.
6) Pragma - The older version of cache-control still used by some old websites. Just set this to the same value as cache-control if you are using it.
7) Referer - Usually, the previous page URL. (Purposely spelt wrong)
8) Cookie - Information stored on your browser is passed to the request.
9) X-CSRF-token/X-CSRF - Used in protecting the request from being forged by other users through link misdirection. These headers are manually added because they are prefixed with 'X-' and usually not sent by the browser, but by the server.
10) `X-Requested-With: XMLHttpRequest` - Usually used when making an AJAX request by the browser.
11) Location - The location header is set by 3XX [status codes](#status-codes). Most HTTP libraries provide the functionality not to follow redirects and view the content behind the page; in certain cases, this can be used to bypass security systems.

> Before it was patched. Solebox had a queue bypass that let you access a product behind a queue by turning off redirects. You could then quickly check out this product as they had no fallback security.

```php
# Solebox example code (PHP)
if ($userHasNotCompletedQueue) {
 # Redirect the user to the queue URL.
 header("location: " . $backToQueueURL);
 # Because they failed to terminate the script the content is behind it is visible when we turn off redirects
 # If the line below was uncommented it would have stopped users getting access to the product page.
 # die("Unauthorized!");
}

echo $productPage;
```


#### Content Types

Content types determine the formatting of your data before sending it to a web server. The three most common types of content types are:

1) JSON - `application/json` - Usually used inside asynchronous requests. Can contain multiple dimensions of information
2) Form encoded `application/x-www-form-urlencoded` - Made by the browser to send key:value information
3) Multipart `multipart/form-data` - Rarely seen, usually used for file transfer (Uploading a video etc.)


#### Cookies

Cookies are small pieces of key:value information stored by a website. This is usually done for tracking and session management. Cookies are often used in security systems to determine if a user is legitimate or a bot. Cloudflare uses two cookies to determine whether or not to allow access to a website: `__cfduid` and `__cf_bm`.

> Thanks for reaching out to us regarding the __cf_bm cookie. This new cookie is part of Cloudflare's Bot Management service and helps manage incoming traffic that matches criteria associated with bots.

Cookies are essential for state-management when creating a bot or monitor. Cookies sometimes store what products you have in your cart (Usually in a non-visible way so the user cannot edit them manually) and whether you're logged in on a website or not.

Not using or incorrect usage of cookies may result in repeating errors. An example of an error would be adding an item to your cart and then not being able to see it in your basket when you visit the checkout page.

I recommend using the [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en) extension to view and edit your cookies in the browser easily; This is an excellent tool for testing cookie issues.


#### Programmable Browsers

Programmable browsers such as [Selenium](https://selenium.dev/) or [Puppeteer](https://github.com/puppeteer/puppeteer) are the go-to for new developers; they're effortless to get started with and can often bypass many security systems when configured correctly without having to do any intense programming or searching for a solution/bypass. Programmable browsers can run Javascript; this means that antibot companies such as Akamai and PerimeterX (which use browser-fingerprinting through Javascript as one of their methods to determine if a real user or a bot) is automatically handled for you.

The issue with programmable browsers in comparison to requests they're incredibly CPU and resource-intensive. The extra resource intensity means on the same computer you may only be able to run 5 puppeteer tasks but run 200 request tasks. Usually, there are more external resources to download which a pure request-based does not have. (Image downloads and Javascript files) making it more data consuming when using data-limited proxies.

It's important you make the choice which is better to use in your project. You may be critiqued for using programmable browsers over HTTP requests as it's seen as less "professional" but it's scenario dependant.


#### Cache

Cache is an incredibly important concept to understand; especially when making monitors. Have you ever been to a webpage and saw sizes, but when you try to add one to your cart it says it is out of stock? That's cache. Cache will show an older, less updated version of the webpage to make results and loading times faster since it can store the data in memory rather than having to query a database (which is **much** faster). We want to try and avoid cache as much as possible, and there are many ways to do this.

How do you check if you're bypassing cache or not? Check the response headers when you make a request. Common returned values for missed-cache are `Cache-Control: max-age=600` and `Cache: MISS`. If you have hit a cached page usually headers will look something more like `Cache-Control: max-age=429` (Meaning the page is 171 seconds old normally) or `Cache: HIT`.

##### How to avoid cache on some websites

All websites are different, trying to avoid cache is usually a long trial-and-error process and will be different for most websites. Certain websites may not use any caching. If your monitor is unusually late, it's usually because you're looking at a cached webpage.

1) The most common way to avoid cache is by randomizing a query parameter at the end of the URL. An example would be turning `https://dash.sneakerbotics.com/` into `https://dash.sneakerbotics.com/?_=RANDOM`.
2) Setting your own `Cache-Control: no-cache` and `Pragma: no-cache` can avoid being returned a cached page on a website.
3) Randomizing specific headers such as `Accept` may result in non-cached results.


#### Proxies

Proxies are used to help make your request more anonymous and reduce ban rates. Proxies are an essential part of monitoring and botting and often considered the worse part because they can cost significant amounts of money. There are two types of proxies. Residential and Datacenter proxies.


##### Residential proxies

Residential proxies are usually botnets meaning when you use one, it will be forwarded through another person's computer without them even knowing. Although immoral, they're great for hiding your identity when connecting to a website since the IPs are randomised and often do not share subnets (Improving anonymity). Residential proxies are usually slower since they have to be forwarded through another person's connection and can range from a few hundred milliseconds to several seconds per request. Residential proxies are usually priced per GB with ranges commonly between $5 and $20 a GB depending on the quality of the proxies.


##### Datacenter proxies

Datacenter proxies are proxies that are forwarded through datacenters, common datacenter are [AWS](https://aws.amazon.com/), [Google Cloud](https://cloud.google.com/), [Azure](https://azure.microsoft.com/en-gb/), [DigitalOcean](https://www.digitalocean.com/) and [Vultr](https://www.vultr.com/). Many of these providers have been banned on most websites by default now because they use commonly seen subnets and IP addresses. Datacenter proxies are extremely fast because they're hosted in datacenters which have little ping and server-sized download speeds, taking as little as 50ms with some providers. Datacenter proxies often share very similar subnets making them more easily detectable. A website may completely ban a subnet after detecting abuse, this can't be done as easily with residential proxies.


##### How to choose?

Proxy type depends entirely on the budget, the website (banning rates, etc.), and the speed required for your monitors. The ideal choice will involve valuing consistency over speed. Certain websites may only restock between working-time and allow you to turn off/increase the delay of the monitors overnight; saving data and helping avoid rate-limiting.