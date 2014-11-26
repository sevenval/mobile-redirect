Mobile Redirect
===============

One of those easily asked but hard answered questions is how to get users to recognize and even better to use your mobile site. The mobile version might not be listed in the search engine result.
If it's unknown one might end up using the default version on a mobile, unhappy about the handling he might never return again.
Another issue are those numerous choices for a mobile Version. Is it m., .mobi, wap., pda., mobile., domain.com/m? If you don't know it, be sure you won't want to try out.
You might want to get them all, but go for one and on the rest set a 301 (permanent redirect) to bundle them up to one mobile address.

Using one domain for all would not only solve the above problem, users originating from different sources (RSS feeds, blogs, twitter ...) and expecting certain content could be served this way as well.

Let´s break it down to one question: What is the best way to tell the user that there is a mobile version? To redirect him, to redirect, offering an option to change back, or let the user decide himself?

Please note that the Mobile Redirect and the user agent detection are *not to restrict* the user but *to support* him. Think about providing a link back to the default site and vice versa. If not, you´ll have to ask yourself if you really know best what content the user wants? A forceful redirect may easily lead to user´s frustration.

##No Redirects##

The advantage is that those who do not want to use the mobile version are not bothered in any way. But others, who might want to use it, have to struggle to get there and might get lost on the way. There should be a link and a note about that feature on the mobile and as well on the default version to switch back and forth.


##Redirect##

First thing to do, if you´ve decided to redirect mobile devices in some form, is to decide to what extend there should be redirection on your site. Is it enough to detect and respond on your entrypage? This assumes that deeper level pages are probably not linked on other sites. Only redirecting your startpage is very easy and not much can go wrong. You only redirect on ```www.example.com```, a call on ```www.example.com/index.html``` or any other page is not effecting in any way. Leave a link to ```www.example.com/index.html``` on your mobile version to get back and you are on the safe side. No cookies need to be set and your mobile version won´t make any decisions that might produce loops.

Sounds good so far? Of course there are some problems to it, too. Only ```www.example.com``` is evaluated. A user would have to bookmark certain pages to get the mobile version right away or he would always have to make that extra click to get there. As already mentioned users originating from different sources (searchengine, blogs, twitter, RSS feeds ...) might already use mobile version of these sites and probably would appreciate it very much to stay in the mobile context.

If you plan redirecting more than only your start page, for example if pages are linked via twitter the whole thing gets bit more complex and if your mobile version differs from your default site it's even a little more. Is it just certain parts that are left out or is it a whole new content structure?

You have to watch out that you are not producing any loops if the user, that has been send to your mobile version, decides not to be mobile and switches back, not using the hopefully offered link to go there. In addition your mobile version probably uses some kind of device detection as well. Even though it might be a desktop that is browsing your mobile version there are about a hundred reasons why a user might prefer the mobile version. So please leave a choice and never redirect from mobile to desktop. Besides the rude patronizing behavior there is a danger of running into some looping problems.

So now that we have talked a lot about advantages and problems in theory, we want to get a little more practical.

##Let´s start with some action##

The first scenario will redirect on ```www.example.com``` and only there. This is the easy way where nothing essentially needs to be stored. The device starts a request on ```www.example.com``` there is some sort of device detection and based on its result the device will be redirected to m.example.com or not.
Any links pointing from mobile to default should now link to ```www.example.com/index.html```. This way you won´t run into a second detection/redirection and avoid any looping problems.
So let´s have a look at the following example for a serverside detection and redirection.


```sh
#################################################################################################
# start mobile section
#################################################################################################
RewriteEngine on
#
#################################################################################################
# notify proxy caches
#################################################################################################
SetEnvIf Request_URI "//?$" MOBILEREDIRECT=on
Header set Vary "User-Agent" env=MOBILEREDIRECT
#
#Header set Cache-Control "public, max-age=60" env=MOBILEREDIRECT
#################################################################################################
# Checking the usual mobile user agents that definitely identify a mobile device.
# Checking if any preference cookie is already set
# For further processing a cookie is set.
#################################################################################################
RewriteCond %{env:MOBILEREDIRECT} ^on$    
# Disable following line if tablets should be treated as a desktop device
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
# start mobile version
RewriteRule ^(.*)$  http://m.example.com [R,L]
```

For clientside action JavaScript is the option we use in the following example. An easy way could be a user agent regex or an even simpler but very effective way is a test on screen-width. You´ll have to keep in mind though that a mobile device doesn´t always "speak" JavaScript, which might cause them some trouble. Think about if you want to take care of those devices as well?


```html
<script>
if (('ontouchstart' in window) || (window.innerWidth / (window.devicePixelRatio || 1)) < 400){
 var mobDomain= "http://m.example.com";
 var mobUrl = window.location.href.replace(window.location.hostname, mobDomain); 
 document.location.href=mobUrl;
}
</script>
```

###Pimp the example with some cookie action###

This first example takes you a huge step into the Mobile Redirect topic but of course there is still a little more that can be done to smoothen the barrier between mobile and desktop version. In the next step we´ll add some cookie handling to our example. Once the device started a request a cookie will be set. The second time the cookie content is checked instead of device detection. *Assure that there is a link that overwrites the cookie to use the preferred version (hopefully it gets used a lot)*.


```sh
#################################################################################################
# start mobile section
#################################################################################################
RewriteEngine on
#
#################################################################################################
# notify proxy caches
#################################################################################################
SetEnvIf Request_URI "//?$" MOBILEREDIRECT=on
Header set Vary "User-Agent" env=MOBILEREDIRECT
#
#Header set Cache-Control "public, max-age=60" env=MOBILEREDIRECT
#################################################################################################
# Checking if any preference cookie is already set
# Checking the usual mobile user agents that definitely identifies a mobile device.
# For further processing a cookie is set.
#################################################################################################
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} !YOURCOOKIENAME [NC]
# Disable following line if tablets should be treated as a desktop device
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

And add the following RewriteRule Blocks:


```sh
# set cookie and start mobile version
RewriteRule ^(.*)$ - [CO=YOURCOOKIENAME:COOKIEVALUE1:.YOURDOMAIN:525600:/,C] 
RewriteRule ^(.*)$  http://m.example.com [R,L]    

# check cookies and start mobile version
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=COOKIEVALUE1 
RewriteRule ^(.*)$  http://m.example.com [R,L]    

# set cookie and start default version 
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteRule ^(.*)$ - [CO=YOURCOOKIENAME:COOKIEVALUE2:.YOURDOMAIN:525600:/,C]
RewriteRule ^(.*)$  http://www.example.com/index.html [R,L]
```

And the JavaScript version:


```html
<script>
if (('ontouchstart' in window) || (window.innerWidth / (window.devicePixelRatio || 1)) < 400)     {    
    var ud=getCookie(YOURCOOKIENAME);
    if (ud==null || ud==""){        
        setCookie('YOURCOOKIENAME',COOKIEVALUE1,365);
        var targetDomain= "http://m.example.com";
    }
    else if(ud.match(/COOKIEVALUE1/)){
        var targetDomain= "http://m.example.com";
    } 
    else {
        var targetDomain= "http://www.example.com/index.html";
    } 
    var targetUrl = window.location.href.replace(window.location.hostname, targetDomain); 
    document.location.href=targetUrl;        
}
function getCookie(c_name){
    var i,name,value,Acookies=document.cookie.split(";");
    for (i=0;i<Acookies.length;i++){
        name=Acookies[i].substr(0,Acookies[i].indexOf("="));
        value=Acookies[i].substr(Acookies[i].indexOf("=")+1);
        name=name.replace(/^\s+|\s+$/g,"");
        if (name==c_name){
            return unescape(value);
        }
    }
}
function setCookie(name,value,days) {
if (days) {
    var date = new Date();
    date.setTime(date.getTime()+(days*24*60*60*1000));
    var expires = "; expires="+date.toGMTString();
}
else var expires = "";
document.cookie = name+"="+value+expires+"; path=/";
 }
 function dontGoMobile(){
 setCookie(YOURCOOKIENAME,COOKIEVALUE2,365);
 var defaultDomain= "www.example.com/index.html";
 var defaultUrl = window.location.href.replace(window.location.hostname, defaultDomain); 
 document.location.href=defaultUrl;
 }
</script>
```

###Use an interstitial###

Another nice way to leave the decision up to the user is to use an interstitial for possible mobile devices.
Send to the interstitial page, make your decision and then go on. This might be a prototype interstitial page.


```html
<html>
    <head>
        <title>Interstitial</title>
        <script>
    function setCookie(name,value,days) {
        if (days) {
        var date = new Date();
        date.setTime(date.getTime()+(days*24*60*60*1000));
        var expires = "; expires="+date.toGMTString();
        }
    else var expires = "";
    document.cookie = name+"="+value+expires+"; path=/";
    }
    function goHere(cName,cValue,cLife,targetDomain){
    setCookie(cName,cValue,cLife);
    var targetUrl = window.location.href.replace(window.location.hostname, targetDomain); 
    document.location.href=targetUrl;
    }
    </script>
    </head>
    <body>
        <div>
            <p>We think you are a mobile device. Would you like to use our mobile site?</p>
            <p><a href="#" onclick="goHere(YOURCOOKIENAME,COOKIEVALUE1,365,'http://m.example.com')">Yes I do want to go mobile!</a></p>
            <p><a href="#" onclick="goHere(YOURCOOKIENAME,COOKIEVALUE2,365,'http://www.example.com/index.html')">No, I do not!</a></p>
        </div>
    </body>
</html>
```

In the serverside detection/redirection have to be made some additions aswell. Exchange the RewriteRule Blocks with the following.


```sh
# if it is mobile - show the interstitial  
RewriteRule ^(.*)$  interstitial.html [R,L]    

# check cookies and start mobile version
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=COOKIEVALUE1 
RewriteRule ^(.*)$  http://m.example.com [R,L]    

# check cookies and start default version
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=COOKIEVALUE2
RewriteRule ^(.*)$  http://www.example.com/index.html [R,L]
```

The next time the device returns the cookie handles the decision/redirection progress; the interstitial is not shown again.

###Redirect everything!?###

The last example is the one that can cause the most problems but reacts to all requests of your site, not only the ```www.example.com``` start request. Once again you´ll have to be aware of loops that might occur. The following example works when mobile- and default version have the same structure which is the case when your default version is the resource for your mobile version.

First you´ll have to adjust first your environment variable

    SetEnvIf Request_URI ".*$" MOBILEREDIRECT=on

And change the following RewriteRule Blocks:


```sh
# set cookie and start mobile version
RewriteRule ^(.*)$ - [CO=YOURCOOKIENAME:COOKIEVALUE1:.YOURDOMAIN:525600:/,C] 
RewriteRule ^(.*)$  http://m.example.com/$1 [R,L]    

# check cookies and start mobile version
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteCond %{HTTP_COOKIE} YOURCOOKIENAME=COOKIEVALUE1 
RewriteRule ^(.*)$  http://m.example.com/$1 [R,L]    

# set cookie and start default version 
RewriteCond %{env:MOBILEREDIRECT} ^on$
RewriteRule ^(.*)$ - [CO=YOURCOOKIENAME:COOKIEVALUE2:.YOURDOMAIN:525600:/,L]
```

###Links for more information and examples###

For more Informations about certain topics you might want to check these links:

* [Apaches mod_rewrite](http://httpd.apache.org/docs/2.0/mod/mod_rewrite.html)
* [Apache RewriteRule Flags](http://httpd.apache.org/docs/2.2/rewrite/flags.html)
* [A JS solution using the useragent](https://github.com/sebarmeli/JS-Redirection-Mobile-Site/blob/master/src/redirection-mobile.js)
* [http://detectmobilebrowsers.mobi/](http://detectmobilebrowsers.mobi/)


# License

MIT