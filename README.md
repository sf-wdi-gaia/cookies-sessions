<!--
Creator: WDI Team, editing by Cory
Market: SF
-->

![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)

# Cookies and Sessions

### Why is this important?
<!-- framing the "why" in big-picture/real world examples -->
*This workshop is important because:*

There are a few ways to ensure that your users experience "continuity" between visits to your website. It's nice if you don't need to log on each time you visit a website. It's nice if a site can remember your progress through an article you were reading. It's nice if  the browser already knows about some this information on page load, rather than waiting for a DB response. We can use cookies for this need! If we don't need the user info immediately, we can use sessions, which we do store in the DB. They can hold much more data, but they're slower to access when loading the page.

### What are the objectives?
<!-- specific/measurable goal for students to achieve -->
*After this workshop, developers will be able to:*

- Describe the request and response cycle's relationship to the stateless web
- Discuss and use an HTTP Cookie in a web application
- Differentiate between an HTTP Cookie and a Session

### Where should we be now?
<!-- call out the skills that are prerequisites -->
*Before this workshop, developers should already be able to:*

- Describe the request response cycle.
- Make HTTP requests.
- Use the Chrome Dev tools.

## Cookies
### What's a cookie?
An HTTP cookie is a [small piece of data](http://stackoverflow.com/questions/4100324/how-many-characters-can-be-stored-in-4kb) sent from a website and stored in a user's web browser. Every time the user loads the website, the browser sends the cookie back to the server in the HTTP Request Header. Cookies are commonly used to track whether a user is logged in or not. They can also be used to record user preferences.

![cookie-monster](http://media0.giphy.com/media/EKUvB9uFnm2Xe/giphy.gif)

Our goal today is to harness the power of cookies. First, to track visitors to our website. And secondly, to track their login "session".

## Reading and Writing Cookies -- Server Side

**Reading & Writing Cookies**:

```ruby
# some_controller.rb

def index
    count = cookies[:visit_count] || 1
    cookies[:visit_count] = count.to_i + 1
end
```

**HTTP Response Header**

This sends a response that looks something like the following:

```
HTTP/1.1 200 OK
Set-Cookie: visit_count=1
Content-Type: text/html; charset=utf-8
Content-Length: 11
Date: Mon, 18 May 2015 07:36:50 GMT
Connection: keep-alive

<html></html>
```

The Cookie is then saved to the browser for localhost:3000. You can view it in the Chrome Developer Console under the "resources" tab.

Here's a look at the cookie from a google page:

![dev tools](https://cloud.githubusercontent.com/assets/6520345/19082351/9e55596a-8a12-11e6-9d66-e644b7df0795.png)

[For more detail, check out the Google Developers page on cookies and the dev tools](https://developers.google.com/web/tools/iterate/manage-data/cookies?hl=en).

Once the cookie is set in the browser, any subsequent request to the website automatically has the following line in the HTTP Request Header:

```
...
  cookie: 'visit_count=1',
...
```

## Reading and Writing Cookies -- Client Side
It's also possible to manipulate cookies on the client-side.

To demonstrate the JavaScript syntax, use the Chrome Developer Console:

``` javascript
document.cookie; // "message=hello"
```

As you can see, the cookie displays as a string! It works differently though. You use assignment (`=`) to add values to this string:

``` javascript
document.cookie = "magic_number=10;"
document.cookie; // "vist_count=2; magic_humber=10;"
```


Does it work for you?! Open your Console:

* Can you add a key-value pair to an existing cookie?

For more on this approach, take a look at [Quirksmode on Cookies](http://www.quirksmode.org/js/cookies.html#ex).

**Additional reading:**
* [Cookies in the Chrome Console](https://developers.google.com/web/tools/iterate/manage-data/cookies?hl=en)
* [HTTP Intro](http://code.tutsplus.com/tutorials/http-the-protocol-every-web-developer-must-know-part-1--net-31177)
* [An Introduction to Cookies](http://code.tutsplus.com/tutorials/an-introduction-to-cookies--net-12482) (php/javascript)

<hr>

# Sessions
Cookies are great, but they're limited in size, and they're hard to work with. If we want finer control, we want sessions!

Sessions are just cookies! But sessions generally store their data in a database (on the server), instead of as a string of key/value pairs (in the client's browser).

Imagine for a moment that we have a fancy quiz-app and we used cookies to store user preferences and the current state of the quiz. Eventually the request header might look like:

```
host: quizful.ly
method: GET
cookie: wrong_answers=7; right_answer=3; current_question=11; GeoIP=US:CA:San_Francisco:37.7909:-122.4017:v4; last_access=31-Aug-2015;
```

Now imagine that, instead of storing all this data in the browser, the server kept it in a database. And everytime someone visits the website for the first time, they're assigned a *globally unique id*, or **guid**. The identifying session cookie is then attached to every HTTP Request so that we know who the visitor is:

```
host: quizful.ly
method: GET
cookie: session=a134vbce34584ibjeapc38;
```

Now, instead of needing to read, parse, and manipulate all the data in the cookie, we can just find the user's session based on their **guid**, and then look up their session info in the database.

Rails makes this super easy for us:

``` ruby
# some_controller.rb
def update
    if wrong_answer
        session[:wrong_answers] = session[:wrong_answers].to_i + 1
    else
        session[:right_answer] = session[:right_answer].to_i + 1
        session[:current_question] = next_question.id
    end
    #...
end
```

#### Resources
* [Rails Guides: Securing Sessions](http://guides.rubyonrails.org/security.html)
* [Cookies, Sessions, & Flash Messages](http://www.theodinproject.com/ruby-on-rails/sessions-cookies-and-authentication)
* [MDN Cookies docs](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)
* [MDN Sessions docs](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)
