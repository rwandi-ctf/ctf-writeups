# Login Bot

So there's this reverse blog where ppl can send posts to admins and only admins can see posts

examining the code there's 2 endpoints which don't need login: `/login` and `/send_post`

when you send a post a bot with admin credentials logs in and sends data via a privileged endpoint
except because "to prevent errors" it tries to login twice
and login lets you redirect to any other page on the site via the `?next=` argument, which we can control

then there's a super interesting endpoint `/url/<id>` which redirects to any link in a sent post

this gives us an exploit route:
1. send a post normally with a link to your website
2. send this post request
```py
requests.post("http://34.124.157.94:5002/send_post", data={
    "url": "/url/<url of your post>?bot_login=1",
    "title": "test",
    "content": "test",
})
```
3. log the admin cookie and login

basically the bot first logs in and then tries to access `/url/<url of your post>` which redirects it to your site

:)
