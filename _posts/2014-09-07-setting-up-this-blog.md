---
title: Setting up this blog
---

Getting Started
---
I've been meaning to start a blog for a while now, if for no other reason than to have a place to collect my thoughts.
Last night, with my wife's encouragement, I decided to go for it. I was already pretty sure I wanted to run Jekyll - I run Wordpress for my wife, and although it runs well it's a bit too heavy for my tastes.
A little more digging into Jekyll and I was sold - simple, speedy sites, written in the text editor of my choice ([Sublime](http://www.sublimetext.com/3)!) or even online ([Dillinger](http://dillinger.io/)).
The clincher was that I could easily host the site on GitHub for free.

To speed things up, I looked for a tutorial. I found several, and worked my through with a few issues along the way.
In the end, the best instructions I found were on [Florian Wolters blog](http://blog.florianwolters.de/educational/2014/04/18/Running_Jekyll_with_GitHub_Pages_using_Windows_8.1_x64_and_Ruby_2.0_x64/).

I don't like programs to clutter up the root of my drive, so I installed Ruby to a different location (C:\programs\Ruby200-x64). The Ruby DevKit was unable to automatically detect this, so I had to modify the devkit's config.yml to the following:

```yaml
---
- C:/programs/Ruby200-x64
```

Once I was through with that, I fired up Jekyll and received the following error:
```
C:/programs/Ruby200-x64/lib/ruby/gems/2.0.0/gems/posix-spawn-0.3.9/lib/posix/spawn.rb:164: warning: cannot close fd before spawn
'which' is not recognized as an internal or external command,
operable program or batch file.
```

I performed some reseach and discovered that the error was caused by incompatibilities with Pygments and Windows. After a couple attempts to fix it (downgrading Pygments to 0.5.0, which caused Jekyll to not work anymore; creating a copy of Python.exe called Python2.exe, which had no effect), I came across the following note on [another Jekyll guide](http://jekyll-windows.juthilo.com/3-syntax-highlighting/):

>After you complete the following steps, Jekyll might still output errors when building or serving sites.
>These shouldn’t cause any real problems though, so you can safely ignore them.

I proceeded to run Jekyll locally and sure enough, the code segments were displaying correctly, so *fingers crossed* the error doesn't seem to be breaking anything.
While looking into Jekyll last night, I came across [Joshua Lande's post](http://joshualande.com/jekyll-github-pages-poole/) on creating a Jekyll blog with Github. I quite liked the layout of his site, so I copied the top menu from his post to start customising my blog.

Post Excerpts
---
I personally can't stand full-length posts on the main page of a blog - I want to see snippets so that I can quickly scan through the posts to see if the author has anything else that might interest me.
Thankfully, Jekyll makes this incredibly simple.
I simply popped open index.html, changed post.content to post.excerpt, and added a "Continue reading..." link:

```html
{% raw %}
  {{ post.excerpt }}
  <a href="{{ post.url }}">
      Continue Reading
  </a>
{% endraw %}
```

Disqus Comments
---
I'd integrated Disqus with my wife's Wordpress, but that was pretty much plug and play. Getting set up for my local Jekyll required a little more work.
I copied the Disqus configuration variables script into _includes/comments.html, added a condition to easily enable or disable comments on a per post basis, and customized the variables:

```html
{% raw %}
{% if page.comments %}
<div id="disqus_thread"></div>

<noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
    {% if site.staging %}
      var disqus_shortname = '{{ site.disqus.staging_shortname }}';
      var disqus_url =  '{{ site.staging_url }}' + '{{ page.url }}';
    {% else %}
      var disqus_shortname = '{{ site.disqus.shortname }}';
      var disqus_url =  '{{ site.url }}' + '{{ page.url }}';
    {% endif %}
    var disqus_identifier = '{{ page.id }}';
    var disqus_title = '{{ page.title }}';

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
{% endif %}
{% endraw %}
```

I wanted to test Disqus in a staging environment, so I enabled swapping out the shortname and url variables depending on whether I had staging enabled or not.
I then added those fields to _config.yml:

```yaml
staging: true

staging_url:      http://mystagingurl.com

disqus:
  shortname: 'my-shortname'
  staging_shortname: 'my-shortname-staging'

```

Lastly, I included the comments on the default post page by adding the line:

```html
{% raw %}
{% include comments.html %}
{% endraw %}

```
To save you a look at the repo, this is how that section looked afterwards:

```html
{% raw %}
<div class="container content">
  {{ content }}

  {% include comments.html %}
</div>
{% endraw %}
```


Final Steps
---
I decided to simplify the post urls from e.g. ```/2014/09/07/setting-up-this-blog/``` to ```/setting-up-this-blog``` as it's much shorter and easier on the eyes.
I also wanted to increase the number of posts on the home page to 10.
To do this, I updated the following lines in _config.yml:

```yaml
permalink:        '/:title'
paginate:         10
```

And with that, this blog was ready to go with this first post that I'd been writing throughout the setup process!
