---
title: "setting up static github pages site with jekyll"
date: 2025-01-04
tags: jekyll 
---

This is how I setup a static Jekyll site hosted with github pages.
I was working on Ubuntu for this.
The code is hosted at [https://github.com/galtay/til](https://github.com/galtay/til)
and the site is hosted at [https://galtay.github.io/til/](https://galtay.github.io/til/).

# Setup Ruby

Install a Ruby version manager.
I used [rbenv](https://github.com/rbenv/rbenv){:target="_blank"}.

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

# Setup Minimal Mistakes Theme

The minimal-mistakes theme by [Michael Rose](https://mademistakes.com/) is great!

 * [main repo](https://github.com/mmistakes/minimal-mistakes)
 * [starter repo](https://github.com/mmistakes/mm-github-pages-starter)
 * [docs](https://mmistakes.github.io/minimal-mistakes)

I began with the starter repo and simply updated the links in `_config.yml` and started writing posts.
In particular I added,

``` yaml
baseurl: "/til"
url: "https://galtay.github.io/til"
```

This is needed when publishing a project based github pages site as opposed to a user based site.
[more here](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#types-of-github-pages-sites).

Posts can be written in markdown and the flavor of markdown used is 
[kramdown](https://kramdown.gettalong.org/syntax.html).

# Run Locally

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

# Publish from git branch

I setup my repo to deploy from the `main` branch.
I did this by navigating to Settings/Pages/Build and deployment and choosing `Deploy from a branch`.
When I'm happy with the local changes I push to the `main` branch and the site is automatically deployed.
