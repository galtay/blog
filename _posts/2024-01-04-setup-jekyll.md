---
title: "setting up static github pages site with jekyll"
date: 2024-01-04
tags: jekyll
---

The basics of setting up a Jekyll site on Ubuntu.

Install a Ruby version manager.
We will use [rbenv](https://github.com/rbenv/rbenv){:target="_blank"}.

```bash
sudo apt install rbenv
```

Add the following init command to `~/.zshrc` and source it.

``` bash
eval "$(rbenv init -)"
```


Configure a specific version of Ruby.

``` bash
rbenv install 3.1.2
rbenv local 3.1.2
rbenv rehash
```

Install bundler

``` bash
gem install bundler
```

Install project dependencies

``` bash
bundle install
```

Run the site locally

``` bash
bundle exec jekyll serve
```


By default it uses the,

 * [kramdown](https://kramdown.gettalong.org/syntax.html) markdown syntax
 * [minima](https://github.com/jekyll/minima) theme



