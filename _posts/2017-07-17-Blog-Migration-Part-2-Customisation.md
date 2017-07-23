---
layout: single
title: "Blog Migration Part 2 - Customisation"
date: 2017-07-17
category: [Blog]
excerpt: "Part 2 of 3 on how I migrated this blog to GitHub Pages"
---
{% include toc %}
## Introduction
I was using Blogger as my platform for hosting this blog. It was ok but things like adding code was a pain to format and get right. I never liked the templates that were available and the editor was pretty basic. I had heard about using GitHub for hosting your website and as I am keen to learn more about GitHub, it seemed like a good idea to see if I could migrate the blog to GitHub Pages. Turns out doing this I learn a lot of new things such as GitHub, Markdown, Jekyll, Visual Studio Code and a little Linux.

This is Part 2 of a three part series:
1. [Part 1 - Setup]({{ site.baseurl }}{% post_url 2017-07-16-Blog-Migration-Part-1-Setup %})
2. Part 2 - Customisation
3. [Part 3 - Workflow]({{ site.baseurl }}{% post_url 2017-07-18-Blog-Migration-Part-3-Workflow %})

In this post I will show how I setup the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) theme to my preferences.

You can view this sites raw Jekyll files on GitHub at [cwestwater/cwestwater.github.io](https://github.com/cwestwater/cwestwater.github.io)

## _config.yml
This is the file that is key to a Jekyll based site.  Think of it as the master control file that can alter the site with global options. The Minimal Mistakes website has a great [Quick-Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/) and we will be looking at the [Configuration](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) section. Lets look at the default _config.yml file:

~~~ yml
# Welcome to Jekyll!
#
# This config file is meant for settings that affect your entire site, values
# which you are expected to set up once and rarely need to edit after that.
# For technical reasons, this file is *NOT* reloaded automatically when you use
# `jekyll serve`. If you change this file, please restart the server process.

# Site Settings
locale                   : "en"
title                    : "Site Title"
title_separator          : "-"
name                     : "Your Name"
description              : "An amazing website."
url                      : # the base hostname & protocol for your site e.g. "https://mmistakes.github.io"
baseurl                  : # the subpath of your site, e.g. "/blog"
repository               : # GitHub username/repo-name e.g. "mmistakes/minimal-mistakes"
teaser                   : # path of fallback teaser image, e.g. "/assets/images/500x300.png"
# breadcrumbs            : false # true, false (default)
words_per_minute         : 200
comments:
  provider               : # false (default), "disqus", "discourse", "facebook", "google-plus", "staticman", "custom"
  disqus:
    shortname            : # https://help.disqus.com/customer/portal/articles/466208-what-s-a-shortname-
  discourse:
    server               : # https://meta.discourse.org/t/embedding-discourse-comments-via-javascript/31963 , e.g.: meta.discourse.org
  facebook:
    # https://developers.facebook.com/docs/plugins/comments
    appid                :
    num_posts            : # 5 (default)
    colorscheme          : # "light" (default), "dark"
staticman:
  allowedFields          : ['name', 'email', 'url', 'message']
  branch                 : "master"
  commitMessage          : "New comment."
  filename               : comment-{@timestamp}
  format                 : "yml"
  moderation             : true
  path                   : "docs/_data/comments/{options.slug}" # "/_data/comments/{options.slug}" (default)
  requiredFields         : ['name', 'email', 'message']
  transforms:
    email                : "md5"
  generatedFields:
    date:
      type               : "date"
      options:
        format           : "iso8601" # "iso8601" (default), "timestamp-seconds", "timestamp-milliseconds"
atom_feed:
  path                   : # blank (default) uses feed.xml

# SEO Related
google_site_verification :
bing_site_verification   :
alexa_site_verification  :
yandex_site_verification :

# Social Sharing
twitter:
  username               :
facebook:
  username               :
  app_id                 :
  publisher              :
og_image                 : # Open Graph/Twitter default site image
# For specifying social profiles
# - https://developers.google.com/structured-data/customize/social-profiles
social:
  type                   : # Person or Organization (defaults to Person)
  name                   : # If the user or organization name differs from the site's name
  links: # An array of links to social media profiles

# Analytics
analytics:
  provider               : false # false (default), "google", "google-universal", "custom"
  google:
    tracking_id          :


# Site Author
author:
  name             : "Your Name"
  avatar           : # path of avatar image, e.g. "/assets/images/bio-photo.jpg"
  bio              : "I am an amazing person."
  location         : "Somewhere"
  email            :
  uri              :
  bitbucket        :
  codepen          :
  dribbble         :
  flickr           :
  facebook         :
  foursquare       :
  github           :
  gitlab           :
  google_plus      :
  keybase          :
  instagram        :
  lastfm           :
  linkedin         :
  pinterest        :
  soundcloud       :
  stackoverflow    : # "123456/username" (the last part of your profile url, e.g. http://stackoverflow.com/users/123456/username)
  steam            :
  tumblr           :
  twitter          :
  vine             :
  weibo            :
  xing             :
  youtube          : # "https://youtube.com/c/MichaelRoseDesign"


# Reading Files
include:
  - .htaccess
  - _pages
exclude:
  - "*.sublime-project"
  - "*.sublime-workspace"
  - vendor
  - .asset-cache
  - .bundle
  - .jekyll-assets-cache
  - .sass-cache
  - assets/js/plugins
  - assets/js/_main.js
  - assets/js/vendor
  - Capfile
  - CHANGELOG
  - config
  - Gemfile
  - Gruntfile.js
  - gulpfile.js
  - LICENSE
  - log
  - node_modules
  - package.json
  - Rakefile
  - README
  - tmp
  - /docs # ignore Minimal Mistakes /docs
  - /test # ignore Minimal Mistakes /test
keep_files:
  - .git
  - .svn
encoding: "utf-8"
markdown_ext: "markdown,mkdown,mkdn,mkd,md"


# Conversion
markdown: kramdown
highlighter: rouge
lsi: false
excerpt_separator: "\n\n"
incremental: false


# Markdown Processing
kramdown:
  input: GFM
  hard_wrap: false
  auto_ids: true
  footnote_nr: 1
  entity_output: as_char
  toc_levels: 1..6
  smart_quotes: lsquo,rsquo,ldquo,rdquo
  enable_coderay: false


# Sass/SCSS
sass:
  sass_dir: _sass
  style: compressed # http://sass-lang.com/documentation/file.SASS_REFERENCE.html#output_style


# Outputting
permalink: /:categories/:title/
paginate: 5 # amount of posts to show
paginate_path: /page:num/
timezone: # http://en.wikipedia.org/wiki/List_of_tz_database_time_zones


# Plugins (previously gems:)
plugins:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-gist
  - jekyll-feed
  - jemoji

# mimic GitHub Pages with --safe
whitelist:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-gist
  - jekyll-feed
  - jemoji


# Archives
#  Type
#  - GitHub Pages compatible archive pages built with Liquid ~> type: liquid (default)
#  - Jekyll Archives plugin archive pages ~> type: jekyll-archives
#  Path (examples)
#  - Archive page should exist at path when using Liquid method or you can
#    expect broken links (especially with breadcrumbs enabled)
#  - <base_path>/tags/my-awesome-tag/index.html ~> path: /tags/
#  - <base_path/categories/my-awesome-category/index.html ~> path: /categories/
#  - <base_path/my-awesome-category/index.html ~> path: /
category_archive:
  type: liquid
  path: /categories/
tag_archive:
  type: liquid
  path: /tags/
# https://github.com/jekyll/jekyll-archives
# jekyll-archives:
#   enabled:
#     - categories
#     - tags
#   layouts:
#     category: archive-taxonomy
#     tag: archive-taxonomy
#   permalinks:
#     category: /categories/:name/
#     tag: /tags/:name/


# HTML Compression
# - http://jch.penibelst.de/
compress_html:
  clippings: all
  ignore:
    envs: development


# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: # true
      share: true
      related: true
~~~

As you can see there is a lot in there. Let's go through the sections I changed.

### Site Settings
In the Site Setting section I set the title, name, description and repository information.  It's all pretty much self-explanatory.

### Site Author
This is the section that populates the links on the left hand part of all pages with links to my Twitter, GitHub and Google Plus pages.  There is a pretty extensive list of options here including some I had to Google.  dribbble anyone?

The tip here is to not enter the full link for say your Twitter page, just enter the usernames:

~~~ yml
twitter: cwestwater

Not

twitter: https://twitter.com/cwestwater
~~~

To add your picture to the left hand column, you fill in the avatar entry. You need to go to your folder structure and create an assets folder. Drop the picture you want to use as your headshot and then enter the link:
~~~ yaml
avatar: "/assests/images/headshoto_photo.jpg"
~~~

We will come back later to _config.yml to configure additional options.

One thing to note. While Jekyll is running, looking for changes it will not process changes to _config.yml. You need to stop Jekyll and restart it to reflect changes. Note this is for _config.yml **only**, other file's changed will be caught by Jekyll on the fly.

## Markdown
I use Markdown, specifically [kramdown](https://kramdown.gettalong.org/index.html) to write content. If you don't know what Markdown is have a look at this [introduction](https://daringfireball.net/projects/markdown/basics). GitHub has a good [cheatsheet](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf) (pdf) available.

First of all, in your local username.github.io folder create a _posts folder.  Any markdown file place in there will be converted to a post. The key here is to put some YAML Front Matter at the top of the post. If you look at the end of _config.yml there is an entry:
~~~ yml
# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: # true
      share: true
      related: true
~~~

This controls the default options for a post. As above it uses the single layout html file in _layouts to control how the page looks. It also shows the information entered in the # Site Author part of _config.yml, how long it estimates it will take to read the post (assuming 200 words per minute), comments are disabled (true is commented out), sharing links at the bottom are enabled, and it will show any similar posts available.

I have not setup commenting (again guess where that is controlled from - _config.yml) and so far, have turned off post sharing but commenting out the share: true line

So when creating a post, the key is to enter these lines right at the top:
~~~ yml
---
layout: single
title: "post title"
date: 2017-07-17
---
~~~
The layout: option tells Jekyll to use single layout format. The title becomes the post title header. Finally, the date: needs to be in the format YYYY-MM-DD.  This becomes the post date. After that you can start entering your markdown content.

Save the file, Jekyll will regenerate the site if it's running, and then you can see your post. If you are working on draft posts you don't want to go live with, create a _drafts folder and drop the Markdown file in there. Run Jekyll with the --drafts option and it will automatically scan the _drafts folder. By default it omits that folder.

## Assets
Sooner or later you will want to put a picture in your blog entry.  This is easy. Drop the image in the /assets/images/ folder. Then in the markdown file use:
~~~ pandoc
![Image Title]({{ site.url }}/assets/images/image.jpg)
~~~
This tells Jekyll to look at the root of the site then browse down to the /assets/images folder then find the image.jpg file. It essentially creates a link to that image and embeds it into the post.

You can do this for any file type. I would recommend for instance creating an /assets/pdf/ folder for pdf's. You get the idea.

## About Page
One thing I wanted at the top of every page was a link to an About page. This was straightforward.

Create a folder called _pages. At the top of the markdown file enter the following:
~~~ yml
---
layout: single
title: "About Joe Bloggs"
permalink: /about/
---
~~~

Next open _config.yml and in the # Default section under #posts add the new entry:
~~~ yml
   # _pages
  - scope:
      path: ""
      type: pages
    values:
      layout: single
      author_profile: true
~~~

This tells Jekyll to use the single layout and add the Site Author information at the left. As it's not something I wanted the extra things like read time, comments, sharing, etc. you don't define them line for #posts

Now to display that About page in the Navigation bar at the top you open /_data/navigation.yml. By default it shows a link to the Minimal Mistakes Quick-Start Guide. Comment out lines 3 and 4 to hide that. To add your about page add a new - title: and url entry. My navigation.yml file now looks like so:
~~~ yml
# main links
main:
  # - title: "Quick-Start Guide"
  #   url: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
   - title: "About"
     url: /about/
  # - title: "Sample Posts"
  #   url: /year-archive/
  # - title: "Sample Collections"
  #   url: /collection-archive/
  # - title: "Sitemap"
  #   url: /sitemap/
~~~

Let Jekyll do it's thing and check. Your about page should be there.

## Custom Domain
As you can see, the URL for your sire is https://username.github.io. Usually you want a custom domain like this one. This is easy to setup in GitHub pages.
1. Open the repostitory Settings page on the GitHub website.
2. Browse to the GitHub pages portion. Enter your domain name in the Custom domain field and click Save ![Custom Domain]({{ site.url }}/assets/images/custom-domain.png)
3. In your Domain DNS settings create a CNAME record and point it towards username.github.io

There are slightly different instructions for different domain URLs so please look at the GitHub help [page](https://help.github.com/articles/quick-start-setting-up-a-custom-domain/)

## Part 3 - Further customisation and Workflow
We covered quite a bit in Part 2. If you are unsure of anything, the Minimal Mistakes [Quick-Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/) is very helpful. I would recommend reading it all. Remember you can always look at this sites files at [cwestwater/cwestwater.github.io](https://github.com/cwestwater/cwestwater.github.io). Feel free to download a copy of the files and see what I have done. If you have any questions. please reach to me.

[Part 3 - Workflow]({{ site.baseurl }}{% post_url 2017-07-18-Blog-Migration-Part-3-Workflow %}) will cover an overview of my workflow in posting a new blog entry.