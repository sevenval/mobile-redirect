Mobile Redirect
===============

One of those easy to ask but difficult to answer questions is how to get users to recognize or even better to use your mobile site. The mobile version might not be listed in the search engine result.
If the user doesn´t know about the mobile site, he might end up using the default version on a mobile and, unhappy about the handling, he might never return.
One issue is the number of possible URLs for a mobile Version. Is it `m.`, `.mobi`, `wap.`, `pda.`, `mobile.`, `domain.com/m` or something else? If you´re uncertain, you´re unlikely to try any of them.
You might want to get them all, but choose one to serve mobile traffic (e.g. `m.example.com`) and on the rest set a 301 (permanent redirect).

Using one domain for all clients would not only solve the above problem, users originating from different sources (RSS feeds, blogs, twitter ...) and expecting certain content could be served this way as well.

Let´s break it down to one question: What is the best way to tell the user that there is a mobile version? To redirect him automatically (plus offering an option to go back), or rather let the user decide what to do?

Please note that the Mobile Redirect and the user agent detection should *not restrict* the user but *support* him. Consider providing a link back to the default site and vice versa. If not, you´ll have to ask yourself if you really know best what content the user wants. Forcing a client to use the mobile or full version with no possibility to change may be frustrating for the user.


## No Redirects

The advantage is that those who do not want to use the mobile version are not bothered in any way. But others, who might want to use it, have to struggle to get there and might get lost on the way. There should be a link and a note about that feature on the mobile and as well on the default version to switch back and forth.


## Redirect

The first step to take after you have decided to redirect mobile devices in some form, is to decide to what extend there should be redirection on your site. Is it enough to detect and redirect on your home page? This assumes that deeper level pages are probably not linked on other sites. Only redirecting your home page is very easy and not much can go wrong. You only redirect on `www.example.com`, a request to `www.example.com/index.html` or any other page is not affected in any way. Don´t forget to leave a link to `www.example.com/index.html` on your mobile version. No cookies are needed and chances for bouncing redirects between the sites are low.

Sounds good so far? Of course there are some problems, too. Only `www.example.com/` is evaluated. A user would have to bookmark certain pages to get the mobile version right away or he would always have to make that extra click to get there. As already mentioned, users originating from different sources (search engines, blogs, twitter, RSS feeds ...) may already use mobile version of these sites and probably would appreciate to stay on the mobile site.

If you plan to redirect more than only your start page, e.g. if pages are deep-linked via Twitter, the whole thing gets a bit more complex. If your mobile and full site do not share the same URLs (read sitemap), it´s even worse. This may give you a hard time calculating the approriate target URL for redirects to the "other site".

You have to take care not to produce any redirect loops. If a user who has been sent to your mobile version wants to use the full version, don´t automatically send him back to mobile. In addition, your mobile version probably uses some kind of device detection as well. Even though it might be a desktop that is browsing your mobile version, there are lots of reasons why a user may prefer the mobile version. So please leave a choice and never redirect from mobile to desktop.

So now that we have talked a lot about advantages and problems in theory, we want to get a little more practical.


## Let´s start with some action

The first scenario will redirect on `www.example.com/` and only there. This is the easy way where no client state has to be tracked. The device starts a request on `www.example.com/`, some sort of client detection takes place and based on the result, the client will be redirected to `m.example.com/` or not.
Any links pointing from mobile to the full site should now link to `www.example.com/index.html`. This way, the user won´t run into a second detection/redirection and therefore avoid any looping problems.
So let´s have a look at the following example for a server side detection and redirection.


```sh
#################################################################################################
# start mobile section
#################################################################################################
RewriteEngine on
#
#################################################################################################
# notify proxy caches
#################################################################################################
SetEnvIf Request_URI ^/$ MOBILESITEREDIRECT=on
Header set Vary "User-Agent" env=MOBILESITEREDIRECT
#
#Header set Cache-Control "public, max-age=60" env=MOBILESITEREDIRECT
#################################################################################################
# Checking the usual mobile user agents that definitely identify a mobile device.
# Checking if any preference cookie is already set
# For further processing a cookie is set.
#################################################################################################
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
# Disable the following line if tablets should be treated as a desktop device
RewriteCond %{HTTP_USER_AGENT} "Android 3.0|Pad|Xoom|Flyer|tablet" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "nokia|iphone|android|motorola|^mot-|^lct|^lg-|\(webos/" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "softbank|foma|docomo|kddi|up\.browser|up\.link" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "htc|dopod|blazer|netfront|helio|hosin|huawei|xda" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "novarra|coolpad|webos|techfaith|palmsource|^vodafone" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "mobile\ssafari|blackberry|alcatel|amoi|ktouch|nexian|samsung|^sam-" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "^lge|ericsson|philips|sagem|wellcom|bunjalloo|maui" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "symbian|smartphone|midp|wap|phone|iemobile|^spice" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "^bird|^zte-|longcos|pantech|gionee|^sie-|portalmmm" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "jig browser|hiptop|^ucweb|^benq|haier|vertuascent" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "opera mobi|opera mini|320x320|240x320|176x220" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "minimo|windows ce|avantgo|ppc; mda|ipod|pocketpc" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "palmos|mobileexplorer|maemo browser|configuration/cldc" [NC,OR]
RewriteCond %{HTTP:X-WAP-PROFILE} "http" [NC,OR]
RewriteCond %{HTTP:PROFILE} "http" [NC]
# start mobile version
RewriteRule ^(.*)$ http://m.example.com/ [R,L]
```

For client side action, we will use JavaScript in the following example. An easy way could be a user-agent regex. An even simpler but very effective way is a test on screen-width. Keep in mind though that not all mobile clients run JavaScript. Do you want to take care of those devices as well?

```html
<script>
if (('ontouchstart' in window) || (window.innerWidth / (window.devicePixelRatio || 1)) < 400){
    var mobDomain = 'http://m.example.com';
    var mobUrl = window.location.href.replace(window.location.origin, mobDomain);
    document.location.href = mobUrl;
}
</script>
```


### Pimp the example with some cookie action

This first example takes you a huge step into the Mobile Redirect topic, but of course there is still a little more that can be done to narrow the gap between mobile and desktop version. In the next step, we´ll add some cookie handling to our example. On the first request, a cookie will be set. Whenever the client returns, the cookie is checked instead of running the device detection. *Make sure there is a link to override the automatic decision. It shoud update the cookie.*.

```sh
#################################################################################################
# start mobile section
#################################################################################################
RewriteEngine on
#
#################################################################################################
# notify proxy caches
#################################################################################################
SetEnvIf Request_URI ^/$ MOBILESITEREDIRECT=on
Header set Vary "User-Agent" env=MOBILESITEREDIRECT
#
#Header set Cache-Control "public, max-age=60" env=MOBILESITEREDIRECT
#################################################################################################
# Checking if any preference cookie is already set
# Checking the usual mobile user agents that definitely identifies a mobile device.
# For further processing a cookie is set.
#################################################################################################
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} !YOURCOOKIENAME [NC]
# Disable the following line if tablets should be treated as a desktop device
RewriteCond %{HTTP_USER_AGENT} "Android 3.0|Pad|Xoom|Flyer" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "nokia|iphone|android|motorola|^mot-|^lct|^lg-|\(webos/" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "softbank|foma|docomo|kddi|up\.browser|up\.link" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "htc|dopod|blazer|netfront|helio|hosin|huawei|xda" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "novarra|coolpad|webos|techfaith|palmsource|^vodafone" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "mobile\ssafari|blackberry|alcatel|amoi|ktouch|nexian|samsung|^sam-" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "^lge|ericsson|philips|sagem|wellcom|bunjalloo|maui" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "symbian|smartphone|midp|wap|phone|iemobile|^spice" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "^bird|^zte-|longcos|pantech|gionee|^sie-|portalmmm" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "jig browser|hiptop|^ucweb|^benq|haier|vertuascent" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "opera mobi|opera mini|320x320|240x320|176x220" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "minimo|windows ce|avantgo|ppc; mda|ipod|pocketpc" [NC,OR]
RewriteCond %{HTTP_USER_AGENT} "palmos|mobileexplorer|maemo browser|configuration/cldc" [NC,OR]
RewriteCond %{HTTP:X-WAP-PROFILE} "http" [NC,OR]
RewriteCond %{HTTP:PROFILE} "http" [NC]
```

And add the following RewriteRule blocks:

```sh
# set cookie and start mobile version
RewriteRule ^(.*)$ http://m.example.com/ [CO=YOURCOOKIENAME:MOBILESITE:.YOURDOMAIN:525600:/,R,L]

# check cookies and start mobile version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=MOBILESITE
RewriteRule ^(.*)$ http://m.example.com/ [R,L]

# set cookie and start default version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteRule ^(.*)$ http://www.example.com/index.html [CO=YOURCOOKIENAME:FULLSITE:.YOURDOMAIN:525600:/,R,L]
```

And the JavaScript version:

```html
<script>
if (('ontouchstart' in window) || (window.innerWidth / (window.devicePixelRatio || 1)) < 400) {
    var ud = getCookie('YOURCOOKIENAME');
    if (ud == null || ud == ''){
        setCookie('YOURCOOKIENAME', 'MOBILESITE', 365);
        var targetDomain = 'http://m.example.com';
    }
    else if(ud.match(/MOBILESITE/)){
        var targetDomain = 'http://m.example.com';
    }
    else {
        var targetDomain = 'http://www.example.com';
    }
    var targetUrl = window.location.href.replace(window.location.origin, targetDomain);
    document.location.href = targetUrl;
}
function getCookie(c_name){
    var i, name, value, cookies = document.cookie.split(';');
    for (i = 0; i < cookies.length; i++){
        name = cookies[i].substr(0, cookies[i].indexOf('='));
        value = cookies[i].substr(cookies[i].indexOf('=') + 1);
        name = name.replace(/^\s+|\s+$/g, '');
        if (name == c_name){
            return decodeURIComponent(value);
        }
    }
}
function setCookie(name, value, days) {
    var expires = '';
    if (days) {
        var date = new Date();
        date.setTime(date.getTime() + (days*24*60*60*1000));
        var expires = '; expires=' + date.toGMTString();
    }
    document.cookie = name + '=' + encodeURIComponent(value) + expires + '; path=/';
}
</script>
```


### Use an interstitial

Another way to leave the decision up to the user is to use an interstitial for possible mobile devices.
The user receives the interstitial page, makes her decision, the result is stored in a cookie and the user
continues on her preferred site. This might be a prototype interstitial page.

```html
<html>
    <head>
        <title>Interstitial</title>
        <script>
            function setCookie(name, value, days) {
                var expires = '';
                if (days) {
                    var date = new Date();
                    date.setTime(date.getTime() + (days*24*60*60*1000));
                    var expires = '; expires=' + date.toGMTString();
                }
                document.cookie = name + '=' + encodeURIComponent(value) + expires + '; path=/';
            }
            function goHere(name, value, life, targetDomain){
                setCookie(name, value, life);
                var targetUrl = window.location.href.replace(window.location.origin, targetDomain);
                document.location.href = targetUrl;
            }
        </script>
    </head>
    <body>
        <div>
            <p>We think you are using a mobile device. Would you like to use our mobile site?</p>
            <p><a href="#" onclick="goHere('YOURCOOKIENAME', 'MOBILESITE', 365, 'http://m.example.com')">Yes I do want to go mobile!</a></p>
            <p><a href="#" onclick="goHere('YOURCOOKIENAME', 'FULLSITE', 365, 'http://www.example.com/index.html')">No, I do not!</a></p>
        </div>
    </body>
</html>
```

The server side detection/redirection code also needs some modifications. Replace the RewriteRule blocks with the following:

<!-- FIXME Check Referrer fuer Clients ohne Cookie-Support?; kein R fuer interstitial.html ?-->

```sh
# if it is mobile - show the interstitial
RewriteRule ^(.*)$ interstitial.html [R,L]

# check cookies and start mobile version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=MOBILESITE
RewriteRule ^(.*)$ http://m.example.com/ [R,L]

# check cookies and start default version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=FULLSITE
RewriteRule ^(.*)$ http://www.example.com/index.html [R,L]
```

The next time the client returns, the cookie handles the decision/redirection process; the interstitial is not shown again.


### Redirect everything!?

The last example is the one that is most likely to cause problems, but it works on all relevant URLs of your site – not only on the home page (`www.example.com/`). Once again, you´ll have to be aware of redirect loops that might occur. The following example works if the mobile and full site have the same site structure.

At first, you´ll have to adjust your environment variable and enable MOBILESITEREDIRECT everywhere:

    SetEnvIf Request_URI ^ MOBILESITEREDIRECT=on

However, this is way too generous. What we actually want is redirects for all the URLs which a user may directly navigate to, links found on other web sites, in social media or received by email; basically everything that will respond with HTML, but not sub requests like CSS, JavaScript, images or other media files.

    # turn off MOBILESITEREDIRECT for obvious sub requests
    RewriteRule \.(css|js|json|png|jpg|jpeg|gif|webp|bmp|svg|svgz|woff|woff2|otf|ttf|eot|swf|pdf|ico|m3u8|mp4|mpg|mov|m4v|m4a|aac|mp3|m3u|map|txt|rss|xlsx|xls|docx|doc|pptx|ppt|vcard|cache)$ - [NC,env=!MOBILESITEREDIRECT]

Your site may be structured such that specific paths serve nothing but assets. You should disable mobile redirects for any such location you can identify.

    RewriteRule ^/(static|assets|gfx|img|images|js|css|fonts|icons|videos|media)/ - [env=!MOBILESITEREDIRECT]

Those locations do not need to be on the top level:

    RewriteRule ^/content/media/images/ - [env=!MOBILESITEREDIRECT]

There are additional criteria you can use to disable the redirect. Only sending the redirect when the request method is GET or HEAD is safe and reliable:

    RewriteCond %{REQUEST_METHOD} !(GET|HEAD) [NV]
    RewriteRule ^ - [env=!MOBILESITEREDIRECT]

If you evaluate any headers (except Host) to determine whether to enable the mobile redirect, you should add the headers´ names to the `Vary` header to ensure that cached content is not reused incorrectly. Apache httpd helpfully does this automatically whenever a header is evaluated in a `RewriteCond`. Note that this negatively impacts the cacheability of your content.

```sh
# set cookie and start the mobile version
RewriteRule ^(.*)$ http://m.example.com/$1 [CO=YOURCOOKIENAME:MOBILESITE:.YOURDOMAIN:525600:/,R,L]

# check cookies and start the mobile version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=MOBILESITE
RewriteRule ^(.*)$ http://m.example.com/$1 [R,L]

# set cookie and start the default version
RewriteCond %{env:MOBILESITEREDIRECT} ^on$
RewriteRule ^(.*)$ - [CO=YOURCOOKIENAME:FULLSITE:.YOURDOMAIN:525600:/,L]
```


## Links to more information and examples

* [Apaches mod_rewrite](http://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)
* [Apache RewriteRule Flags](http://httpd.apache.org/docs/2.4/rewrite/flags.html)
* [A JS solution using the user-agent](https://github.com/sebarmeli/JS-Redirection-Mobile-Site/blob/master/src/redirection-mobile.js)
* [http://detectmobilebrowsers.mobi/](http://detectmobilebrowsers.mobi/)


# License

MIT
