---
layout: post
title:  "Personal site for free with Github pages and Jekyll"
date:   2020-09-27 16:42:38 +0200
categories: blog
---
This is not the first blog that I opened during the last 10 years. I tried multiple times with different technologies: Worpress and Joomla. I was spending more time on settings, themes and configurations than on content.

But over time you become more wiser, so this time I decided to focus on the content and not on technology. I found the combination of Github pages and Jekyll the best match for my purpose. In this article I want to show how easy is to setup a personal blog/site without lot of effort expecially if you have experience with GitHub ad Markdown. 

# Git Hub pages
> Websites for you and your projects.
Hosted directly from your GitHub repository. Just edit, push, and your changes are live.

Created mainly for website projects for the repositories, they are more and more used also as personal site. Creating it is very simple:

* Go to [GitHub](https://github.com/)
* create a new public repository named ***username*.github.io**, where username is your username on GitHub.
* Clone your repo 
``` bash
git clone https://github.com/username/username.github.io
```
* add an index.md and push!

and that's it! You can now access to your site at https://username.github.io
The index page of your website can be a index.md file or a README.md file. If both exists the index.md file has priority. You can also put html, css and also js directly. For a full features refer to official [docs](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/getting-started-with-github-pages).


If you want more power use [Jekyll](https://jekyllrb.com/)!
# Jekyll
> Transform your plain text into static websites and blogs.
* Simple: No more databases, comment moderation, or pesky updates to install—just your content.
* Static: Markdown, Liquid, HTML & CSS go in. Static sites come out ready for deployment.
* Blog-aware: Permalinks, categories, pages, posts, and custom layouts are all first-class citizens here.
    
[![Simplicity = Love by bemorewithless](https://bemorewithless.com/wp-content/uploads/2015/10/simplicity.jpg "Simplicity = Love by bemorewithless")](https://bemorewithless.com/lovequotes/)

I think there is nothing to add here. 
# Install on Windows
Yes, yes..I am using Windows. Why not? Expecially in the last years is not so bad. I wrote an [article](https://www.linkedin.com/pulse/chocolatey-cmder-how-feel-less-lack-shell-windows-mario-fiore-vitale/) and maybe I will wrote another in this blog. 

A part from that, you can install Jekyll on Windows in two main way:
### Standard installation
In this case you need to install RubyInstaller since Jekyll is based on the ruby language. You can follow the [guide](https://jekyllrb.com/docs/installation/windows/)
I have to admit, I haven't tried it.
### Using WSL
You can use the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10), but the steps described in the guide on Jekyll site not worked for me so I just followed the installation for Ubuntu
``` bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

``` bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

``` bash
gem install jekyll bundler
```
### How to create a site
1. Create a new Jekyll site at ./myblog.
``` bash
jekyll new myblog
```
2. Change into your new directory.
``` bash
cd myblog
```
3. Build the site and make it available on a local server.
``` bash
bundle exec jekyll serve
```
4. Browse to http://localhost:4000
# Themes
* github default themes
* remote themes
* theme overriding
# Limitations
You can not use all plugins but the only github compatibles
