---
layout: post
title: LinkedIn's ShareArticle API
---

If you have found yourself in the unfortunate position of having to use LinkedIn's shareArticle API to share a URL, you may notice that there is around zero articles detailing exactly how to utilize the API correctly.  Even LinkedIn doesn't have an official developer support page which details exactly how to use their shareArticle API.

Lucky for you I recently had to deal with this horribly under-documented share functionality at work and am going to outline exactly what to do to get it working!

Theoretically the shareArticle API should work like this, and actually this is how you want to use it:

{% highlight ruby %}
    www.linkedin.com/shareArticle?mini=true&url=http://www.mycoolwebpage.com
{% endhighlight %}

You may notice that if you try this with your webpage, LinkedIn doesn't gather all the correct information about your page, or possibly it doesn't gather any information about your page except for its title and url.  Where is the thumbnail?  And where is the description?  Lucky for you there's no documentation describing how you can get LinkedIn to pull that information save for a couple forum postings...that have no helpful information.  But the shareArticle API also allows you to pass some GET params to the shareArticle API - to trick you, you see, into doing it the wrong way.  For example, you can specify title, description, source and a couple other things (ooh this looks like it solves our problem!):

{% highlight ruby %}
    www.linkedin.com/shareArticle?mini=true&url=http://www.mycoolwebpage.com&title=mycoolTitle
    &source=dummysource.com&desc=blah%20blah%20blah...
{% endhighlight %}

Except...that if you try sending that URL to LinkedIn and your url-encoded URL is longer than approx. 700 characters, the LinkedIn script throws a Javascript error on IE6&7 on Windows XP, only!  Because this is a Javascript error on the LinkedIn side, you have zero control over how it can be fixed, and if you are dealing with a dynamic length description or title, or source url, constructing this URL can be extremely problematic.

## Solution


Ignore LinkedIn's red-herring extra GET parameters, and just specify the url GET parameter.  This will cause LinkedIn to ping back on your URL and look for these tags (title, meta description, img...):

{% highlight html %}
<html>
  <head>
    <title>The Title!</title>
    <meta name="description" content="This is the description of your linkedIn Article!">
  </head>
  <body>
    <img src="/my/img/path.jpg">
    <img src="/my/img/path2.jpg">
  </body>
</html>
{% endhighlight %}

If you're having trouble getting the images to work because your page is so cluttered, you can setup your page to skim the browser user-agent and look for 'LinkedInBot', and display an altered form of the webpage for LinkedIn specifically.
