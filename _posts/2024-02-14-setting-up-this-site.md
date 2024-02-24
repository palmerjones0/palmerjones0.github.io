---
title: "Setting Up This Site"
excerpt_separator: "<!--more-->"
categories:
  - tutorial
  - blog
tags:
  - Jekyll
  - hosting
  - blogging
  - tutorial
  - GitHub
  - PorkBun
date: 2024-02-14 10:00:00
timezone: America/New_York
search: true
toc: true
---

I wanted to use the first post as a way to share how I set up this site. I was able to host this static site for an average cost *less than $1/month*.

## Choosing a Hosting Platform
This site is a personal project that I don’t expect to produce any income, so my emphasis first and foremost was to choose a low-cost hosting option. From my research, the two main contenders in this category are **GitHub Pages** and **WordPress**. I decided to use GitHub Pages because it is completely free to host a public site, whereas it seemed like anything beyond the most basic WordPress site would incur a monthly fee in the $3-$30 range. Also, I think GitHub Pages is much better suited for building coding skills and showing off programming projects, and overall allows more control while still being simple enough for a novice like me. I’ll go into more detail later, but the main skills you’ll need to get up and running are a basic familiarity with git and some general knowledge on formatting with **Markdown**. GitHub Pages will translate your Markdown formatting into static html via a tool called Jekyll. Fortunately, if you're starting from a community-created theme like I am, you don't really need to know all the ins and outs of Jekyll to create your site.
>Hosting cost per month on GitHub Pages: **$0**

## My Custom Domain
While hosting on GitHub Pages is free, your domain name will be `username.github.io` unless you acquire and connect a **custom domain name**. For me, I really wanted to have a custom `.com` domain for the site. I researched domain name providers and settled on **PorkBun**, where I was able to purchase the palmercjones.com domain on a multi-year contract for an average cost of just under $1 per month. I have had a great experience with PorkBun thus far, and they made the process of connecting my domain to my GitHub Pages site incredibly easy. I’ll go into more detail on how to configure your domain further down in this post, but if you have decided to stick to the default GitHub Pages domain then you can skip the steps about configuring the host.
>Custom domain cost per month: **<$1**

## Getting Started
Follow these steps to get your website up and running.

### Get Your Website Up and Running
1. Create a GitHub account
2. Follow [these instructions](https://docs.github.com/en/pages/quickstart) from GitHub on how to set up a pages site
<br><br>
I recommend also following the instructions to secure your site with `https`.
3. Copy the Minimal Mistakes [sample template](https://github.com/mmistakes/mm-github-pages-starter) repository to your repository
<br><br>
Congrats! You now have a static site accessable from `username.github.io`. If you wish to configure a custom domain, follow the instructions below.

### Add a Custom Domain
1. Purchase a domain from a domain hosting site, I used [PorkBun](https://porkbun.com)
2. Follow [these instructions](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages) to get your domain verified and configured
<br><br>
The key step here is configuring your `CNAME` file to point to your domain, and making sure you follow all of the instructions to make sure your domain is secure. I am not an expert in the field of domain name ownership!

### Customize Your Site
1. Customize your Minimal Mistakes theme using one of the provided [skins](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#skin)
2. Design your sidebar by editing your `_config.yml` file using Markdown
3. Create your About page, here's [mine](/about/) for inspiration, also using Markdown
<br><br>
Now you're ready to write your first post! You'll find templates to go by in your `_posts` folder. The main language you need to learn is Markdown, which is a super simple language that Jekyll will convery to html. Good luck!

## Helpful Resources
* [My GitHub Pages Repo](https://github.com/palmerjones0/palmerjones0.github.io)
* [Minimal Mistakes Jekyll samples](https://github.com/mmistakes/jekyll-sample-content)
* [Minimal Mistakes Cheat Sheet](https://www.fabriziomusacchio.com/blog/2021-08-11-Minimal_Mistakes_Cheat_Sheet/#kramdown)
* [Markdown Guide](https://www.markdownguide.org/basic-syntax/)
