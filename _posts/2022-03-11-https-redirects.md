---
description: How to redirect an apex domain, or subdomain to a service that doesn't provide redirects.
tags: [ web development, administration, infrastructure, devops ]
---
# HTTPS Redirects

I've had to deal with the issue of redirecting a domain on multiple platforms, and it was a pain until today.

Despite the silly name, [Redirect Pizza](https://redirect.pizza) is amazing. It does just this one thing, and it does it flawlessly. I had my domain redirected in under 5 minutes.

## Redirect Pizza

This service is simply elegant. Their landing page asks where you want to redirect from, and where to. It's just two input boxes and a submit button. They then ask you to create an account using a variety of popular platforms, or your email address, and then you're presented with instructions for modifying your DNS record to complete the redirect setup.

It even verified that I had finished my DNS configuration within a minute of my making the change; no page refresh required. Although I did have to refresh the page to see the overall domain status update to completed.

I'm using this service for the blog you're looking at right now. It used to be hosted on Blogger at https://blog.carlinscott.com, but thanks to Redirect Pizza, it's hosted here on GitHub Pages.

## GitHub Pages

This site is hosted on GitHub Pages, which only provides apex domain and https redirect: 

1. http://carlinscott.com > httpS://carlinscott.com
2. https://carlinscott.com > https://www.carlinscott.com

## AWS

You can do http > https redirects on most AWS services, but the other kinds of redirects are more difficult.

The biggest issue with AWS is that they have no solution for redirecting an apex (aka bare, naked) domain that doesn't require you to transfer DNS control to them from your domain registrar (GoDaddy, NameCheap, etc). 

I worked around that issue by deploying a public EC2 server to handle the redirect using ngnx. But it has been a pain to keep the Let's Encrypt cert up to date. I have a cron-job set up to renew the cert every 2 months, but it runs, throws no errors, and doesn't renew the cert. But when I run it manually, it works perfectly.

That solution costs $6/mo.

If you can forward DNS to AWS, then you can use a Load Balancer Listener Rule to perform whatever redirects you need.

This costs minimum $18/mo, but if you're using this solution, you probably already needed a load balancer. So it's basically free.

## Naked SSL

This service looks if simpler than Redirect Pizza, but it's not as easy to use, and they provide little to no info about their service without signing up. I used their domain redirect tester without signing up, and it said that it didn't support my domain without providing an explanation.

I don't recommend this service.

## Redirection IO

This service is really complicated to use. I spent about an hour trying to set up my free redirect from blog.carlinscott.com to www.carlinscott.com, and I gave up. They require you to write rules to match the entire URL using their weird syntax that I couldn't figure out. They could have just let me use Regex, or provided a catch-all example, but they didn't. I also wasn't sure how to deploy my solution after I figured out the matchers.

I think this service could be useful for admins who are managing huge and complex networks of related websites. Their redirect matching engine is sophisticated and provides custom verification scenarios so that you can ensure that it will work the way you want.

I only recommend looking at this service for enterprise IT architects.

## 301 Redirect Website

This is a free service that only provides HTTP redirects. So not terribly useful in the age of HTTPS Everywhere.
