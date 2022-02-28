---
description: How to create robots.txt using ASP.NET Core Razor Pages to hide pages and sites from search results
permalink: /2020/02/hiding-pre-production-aspnet-sites-from.html
tags: [asp.net core, razor pages, security ]
---
# Hiding pre-production ASP.NET sites from robots (search engines, web crawlers)

As software developers, we love to put things out into the world for people to see and play with. Sometimes there's a manager behind us, or a business analyst in front of us asking us to hurry up.  
  
This drive to deploy can lead to us skipping some important steps, such as preventing search engines from publishing our test and demo environments. To avoid this, we can employ two different tactics.  
  

## 1. robots.txt

This is a file that has been around for ages with a singular purpose; to tell robots what to do when they encounter your website. Not all robots listen to our instructions, but we don't necessarily need to worry about those ones. The most problematic robots behave well but have a big impact on us. These robots are search engine web crawlers.  
  
When a search engine crawls your website, it publishes your website to people searching for things related to your site. This may not impact you, but you could end up with a real customer accidentally visiting your beta or test site on accident. You may also end up with a lot more curious people visiting your test site than you'd like.  
  

### ASP.NET robots.txt.cshtml

If you're using a modern version of ASP.NET, you can create a simple Razor Page that will tell robots to go elsewhere for your non-production sites.  
  
This file should be placed with your other Razor Pages in the Pages directory in your ASP.NET website project. You only need to have MVC enabled in Startup.cs to use this, and it doesn't require any controller logic because Razor Pages are magical.  
  
What this Razor Page does, is generate a robots.txt file in your website's root folder. For non-production environments, the robots.txt file tells robots to avoid interacting with the entire site. For production, it tells them to avoid crawling the /hidden/path, which is just a placeholder for any routes on your site that you don't want to be indexed by a search engine.  

{% gist 08a8c3341b0ee85a9ffd350ba51ae056 robots.cshtml %}  

## 2. Web Access Firewall (WAF)

You may be more worried about security bugs on your non-production websites, than them showing up in search results. You may also have an admin portal that you only want accessible from your coworkers and business partners.  
  
The best way to protect sensitive areas on your website is definitely not a robots.txt. For one thing, it doesn't prevent a person or a robot from entering the restricted area, rather it just tells them that they souldn't. It's like a realy low-key keep out sign. The other problem with highlighting a restricted route with robots.txt is that it highlights the sensitive area for hackers to exploit.  
  
A WAF can restrict access to sections of your website to specific blocks of IPs. It is quite common for companies to have a section of their website only available to people on their network. To accomplish this, they create a WAF routing rule that only allows their block of IP addresses from accessing certain routes.  
  
I will not go into the details of doing this as it is dependent on where you're hosting your website, and the web host platform you are using. However, I think the terminology I have provided will help you find what you need on the internet.