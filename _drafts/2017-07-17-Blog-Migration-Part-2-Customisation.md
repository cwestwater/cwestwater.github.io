---
layout: single
title: "Blog Migration Part 2 - Customisation"
date: 2017-07-17
---
## Introduction
I was using Blogger as my platform for hosting this blog. It was ok but things like adding code was a pain to format and get right. I never liked the templates that were available and the editor was pretty basic. I had heard about using GitHub for hosting your website and as I am keen to learn more about GitHub, it seemed like a good idea to see if I could migrate the blog to GitHub Pages. Turns out doing this I learn a lot of new things such as GitHub, Markdown, Jekyll, Visual Studio Code and a little Linux.

This is Part 2 of a three part series:
1. [Part 1 - Setup]({{ site.baseurl }}{% post_url 2017-07-16-Blog-Migration-Part-1-Setup %})
2. Part 2 - Customisation
3. Part 3 - Workflow

In this post I will show how I setup the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) theme to my preferences.

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

As you can see there is a lot in there. Lets go through the sections I changed.

### Site Settings
In the Site Setting section I set the title, name, description and repository information.  It's all pretty self explanatory.

### Site Author
This is the section that populates the links on the left hand part of all pages with links to my Twitter, GitHub and Google Plus pages.  There is a pretty extensive list of options here including some I had to Google.  dribbble anyone?

The tip here is to not enter the full link for say your Twitter page, just enter the usernames:

~~~ yml
twitter: cwestwater

Not

twitter: https://twitter.com/cwestwater
~~~