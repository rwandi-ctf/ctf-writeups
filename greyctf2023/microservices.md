# Microservices

So there's this guy who has a bunch of microservices (docker containers)

looking at the `docker-compose.yml`, there's 3 services: `admin`, `homepage` and `gateway`

only `gateway` is accessible from outside and basically acts as router to the other microservices, it passes only the query parameters

`admin` is a "work in progress" site, and to access it you must pass `?service=admin_page` to `gateway`, but it also checks if `?service` is `admin_page` and denies access if it is which is quite funny

if `?service` is not `admin_page`, however, it will access a website provided in `?url` with the admin cookie

`homepage` has the flag but will only show it you have the admin cookie

any ideas?

*Parameter Pollution*

`admin` uses FastAPI and `gateway` uses flask. if `?service` is provided twice, flask takes the first one, FastAPI takes the last one

so just access the url
```
34.124.157.94:5004/?service=admin_page&service=1&url=http://home_page
```
and you get the flag

:)