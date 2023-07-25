# signpost
> Tracking down pictures can be hard. Surely you can't find where this giant ballpark is...

> Submit your flag as ictf{latitude,longitude}, with the coordinates of where the picture was taken rounded to three decimal places. Example: ictf{-42.443,101.333}

> https://imaginaryctf.org/r/sZAdu#signpost.png

![signpost](https://imaginaryctf.org/r/sZAdu#signpost.png)

We are given an image of a signpost with three signs.

But these signs are weird: what signpost would point to a place that is 2 miles away and a place that is 2,925 miles away?

With the idea that this sign is special, we google search "polo grounds 2925 miles" and this is the first result:

[Ballpark Roadtrip: AT&T Park](https://www.cbsnews.com/news/ballpark-roadtrip-att-park/)

This is, indeed a ballpark. Now we find AT&T Park (which has been renamed to Oracle Park) on Google Maps, which gives us the coordinates: from 37.777 to 37.780 degrees of latitude, and from -122.391 to -122.388 degrees of longitude.

Now all that is left is to try all 16 possible flags.