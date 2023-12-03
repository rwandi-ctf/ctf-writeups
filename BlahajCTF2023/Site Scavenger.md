# Site Scavenger

category: web

> After making a website on my favourite stuffed toy, the Blahaj, I made a flag and hid it somewhere on the website. But now I cannot find it! Please help me get my flag again 😢

We are given a website, with an about page, and a link to the IKEA store. In the About page, if we inspect source, at the bottom of the document, we find the first part of the flag:
```html
</body>

<!-- Oops, this is where I left the first part of the flag. -->
<!-- blahaj{i_l0v3_b14 -->
<!-- If only I knew where to find the other parts of the flag... -->

</html>
```

Next, if we go to `/robots.txt`, we find the second part of the flag:
```
Oops, this is where I left the second part of the flag.
haj_and_y0u_shou
If only I knew where to find the other parts of the flag...
```

The last part was harder. I didn't know where the next part could be, but after ~~dirbusting~~ a lot of trial and error, I found `/sitemap.xml`:
```xml
<sitemapindex>
    <sitemap>
        <loc>/</loc>
    </sitemap>
    <sitemap>
        <loc>/about</loc>
    </sitemap>
    <sitemap>
        <loc>/sitemap.xml</loc>
    </sitemap>
    <sitemap>
        <loc>/sup3r-s3cr3t</loc>
    </sitemap>
    <sitemap>
        <loc>/robots.xml</loc>
    </sitemap>
</sitemapindex>
```

We see an extremely suspicious `/sup3r-s3cr3t` endpoint. However when we went there they greeted us with:

```html
<p>GET functionality still in development</p><br><!-- TODO: Make this page request automatically POST -->
```

So I did a `POST` request to `/sup3r-s3cr3t`, I received:

```
What is your fav_plush?
```

Just how many layers do you want this third part to be????

Anyways after fiddling with url parameters, Headers, and json, I submited a `x-www-form-urlencoded` with `fav_plush: blahaj` and I got the flag:

```html
            Welcome back, here is the 3rd part of the flag.<br>
            <code>1d_70o_^_^}</code>
            <br>Did you remember to collect the other 2 on the way?
```

Putting it all together: `blahaj{i_l0v3_b14haj_and_y0u_shou1d_70o_^_^}`