I've been meaning to start a blog for a while now, if for no other reason than a place to collect my thoughts.
Last night, I decided to go for it. I was already pretty sure I wanted to run Jekyll - I set up Wordpress for my wife, and although it runs well it's a bit too heavy for my tastes.
A little more digging into Jekyll and I was sold - simple, speedy sites, written in the text editor of my choice ([Sublime]!).
The clincher was that I could easily host the site on GitHub for free.

To speed things up, I looked for a tutorial. I found several, and worked my through a few issues along the way.
In the end, the best instructions I found were here: http://blog.florianwolters.de/educational/2014/04/18/Running_Jekyll_with_GitHub_Pages_using_Windows_8.1_x64_and_Ruby_2.0_x64/
I installed Ruby to a different location (C:\programs\Ruby200-x64), so when I installed the DevKit I had to modify my config.yml to the following:

```sh
---
- C:/programs/Ruby200-x64
```

Once I was through with that, I fired up Jekyll and received the following error:
```
C:/programs/Ruby200-x64/lib/ruby/gems/2.0.0/gems/posix-spawn-0.3.9/lib/posix/spa
wn.rb:164: warning: cannot close fd before spawn
'which' is not recognized as an internal or external command,
operable program or batch file.
```

I performed some reseach and discovered that the error was caused by incompatibilities with Pygments and Windows. After a couple attempts to fix it (downgrading Pygments to 0.5.0, which caused Jekyll to not work anymore; creating a copy of Python.exe called Python2.exe, which had no effect), I came across the following note [here](http://jekyll-windows.juthilo.com/3-syntax-highlighting/):

>After you complete the following steps, Jekyll might still output errors when building or serving sites.
>These shouldn’t cause any real problems though, so you can safely ignore them.

I proceeded to run Jekyll locally and sure enough, the code segments were displaying correctly, so the error doesn't seem to be breaking anything.


[Sublime]:http://www.sublimetext.com/3
