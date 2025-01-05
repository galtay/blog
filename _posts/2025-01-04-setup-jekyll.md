---
title: "[til] setting up jekyll site deployed to github pages"
date: 2025-01-04
tags: til jekyll minimal-mistakes
---

This is how I setup a static [Jekyll](https://jekyllrb.com){:target="_blank"}
site hosted with 
[github pages](https://pages.github.com){:target="_blank"}.
The code is hosted at 
[https://github.com/galtay/til](https://github.com/galtay/til){:target="_blank"}
and the site is hosted at 
[https://galtay.github.io/til/](https://galtay.github.io/til/){:target="_blank"}.

# Setup Ruby

Install a Ruby version manager.
I used [rbenv](https://github.com/rbenv/rbenv){:target="_blank"}.
I was working on Ubuntu so I used apt,

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

The minimal-mistakes theme by [Michael Rose](https://mademistakes.com/){:target="_blank"} is great!

 * [main repo](https://github.com/mmistakes/minimal-mistakes){:target="_blank"}
 * [starter repo](https://github.com/mmistakes/mm-github-pages-starter){:target="_blank"}
 * [docs](https://mmistakes.github.io/minimal-mistakes){:target="_blank"}

I began with the starter repo and simply updated the links in `_config.yml` and started writing posts.

Posts can be written in markdown and the flavor of markdown used is 
[kramdown](https://kramdown.gettalong.org/syntax.html){:target="_blank"}.

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

# Deploy from git branch

I setup my repo to deploy from the `main` branch.
I did this by navigating to [Settings] -> [Pages] -> [Build and deployment] and choosing `Deploy from a branch`
with the `main` branch chosen.
When I'm happy with the local changes, I push to the `main` branch and the site is automatically deployed.
