---
layout: post
title:  "Building An Admin Site with Webpy"
date:   2013-09-02 00:00:05
categories: python
---

I participated in a security project as a web developer 2 weeks ealier. The project is about the security door control. It uses a particular sound derived from the user's password to get the permission. My responsibility is to build an admin website. The site should support add, edit, delete users and show the log (security door using history).

This article records the difficulties I met in building the website.

I don't mean to use web.py at first, but since another team member has already used it to route the index page, I decide to give it a try. The database I choose is mongodb. I've used redis before, so this time I want to choose another big brother in NoSQL.

### Session

The first one is how to apply session with web.py and mongodb. The official tutorial doesn't show anything about NoSQL database. After doing some search in Google, I found a [repo][mongo_session]. With the [session tutorial][webpy_session] in web.py, it is not difficult to get the job done. But I spent some time setting and testing whether the session timeout works.

### Date format

When developing the CRUD operation about users, the only problem I have is the user create time. I choose to use `new Date()` when inserting, this is a `ISODate` object. It is something like "2013-08-29T13:27:34.809Z". It is ugly and not understandable for a normal user. What I think first is how to output it in a good format, maybe find the year, month, day, time substring and then create a new, good look string. Ocationally, I found that in Python's `time` module, there is a function called `ctime()`, it converts the ISODate into a string: "Wed Aug 28 16:04:57 2013". It is really perfect!

### CSS

After finishing the simple business logic, I start to make it beautiful. I switch the default template system to Jinja2 first. [Jinja2][jinja] is a wild-used template and really easy to use. Then is to choose a CSS framework. Bootstrap is of course the first choice for a developer who don't have much knowledge in CSS and Javascript. I used the lastest bootstap 3.0 at first, but found the default theme is ugly, so I move to the theme: [Flat-UI][flat-ui], a popular theme I heard of in Hacker News before. The Flat-UI is using bootstrap 2.3.2, so I change to that version. To be honest, CSS is not difficult, and the website is very simple. It doesn't take me too long to get the good effect actually.

### AJAX

Now comes the most difficult part: Javascript. Bootstrap has already provide a bunch of interactive js effects, but what I need is the AJAX to deal with the user's authentication. I haven't learnt that before. I searched Google for a lot of time and finally understand how it works. For the _login_ page, all I need to do is sending the _username_ and _password_ to the server by using AJAX. In the server side, web.py still dealing with the _username_ and _password_ info in the `POST` method. But instead of return a new page, we need to return json data. AJAX in the login page can show different behavior according to the returned json data.

The following code is the front-end AJAX:

{% highlight javascript %}

$.ajax({
  type: "POST",
  dataType: "json",
  data: {
    username : username_str,
    password : password_str,
  },
  success: function(response) {
    if (response.message == "nonexist") {
      $('#alert').removeClass().addClass('alert alert-error').text('User doesn\'t exist!');
      $('input[type=text]').val('');
      $('input[type=password]').val('');
    } else if (response.message == "nomatch") {
      $('#alert').removeClass().addClass('alert alert-error').text('Wrong password!')
      $('input[type=password]').val('');
    } else if (response.message == "nopermission") {
      $('#alert').removeClass().addClass('alert alert-error').text('You are not an admin')
    } else {
      $(location).attr('href', '/admin');
    }
  },
});
    
{% endhighlight %}

This is the back-end code:

{% highlight python %}

def POST(self):
  post = web.input(_method='POST')

  user = users.get_user_by_name(post['username'])
  if user is None:
    response = {'message': 'nonexist'}
  elif user['password'] != post['password']:
    response = {'message': 'nomatch'}
  elif user['authority'] == 1:
    response = {'message': 'nopermission'}
  else:
    users.login(user)
    response = {'message': 'success'}
    logs.login(post['username'])

  return json.dumps(response)

{% endhighlight %}

### Server Sent Event

The website is almost done so far, until I show the site to the team leader… He pointed out that we need a new feature. When someone open the door, the website should pop up a dialog says who open the door. This is about the server-side push tech. And I don't want to use the old AJAX way to do so. I try to find some python support websocket library, but find that websocket need a particular server, not a normal server like apache. Then I find Server Sent Event (SSE). It is about server push message to client, not a socket between client and server. This is exactly what I need.
[This article][sse] explains this technology quite well.

This is my code:

{% highlight python %}

# Default status is close
door_status = 0

class Sse:
  def GET(self):
    web.header('content-type', 'text/event-stream')
	web.header('Cache-Control', 'no-cache')
	while True:
	  if door_status == 1:
	    yield 'data: %s\n\n' % (json.dumps({'door': 'open'}))
    	door_status == 0
	  time.sleep(1)

{% endhighlight %}

The website is still under construction, so I just put a screenshot here. After it is done, I will open source it in GitHub.

![index_page](/assets/img/audio_recorder.png)


[mongo_session]:https://github.com/whilefalse/webpy-mongodb-sessions
[webpy_session]:http://webpy.org/sessions/
[flat-ui]:http://designmodo.github.io/Flat-UI/
[jinja]:http://jinja.pocoo.org/docs/
[sse]:http://www.html5rocks.com/en/tutorials/eventsource/basics/
