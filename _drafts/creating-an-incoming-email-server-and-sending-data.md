---
layout: post
title: Creating an Incoming only email server + send data to an application server
categories: []
image: assets/images/successful-people.jpg
tags: []

---
This is a quick writeup on what I was doing for the past couple of day and night. So, possibly this could be a wrong approach or something that is easily hackable or whatsoever. Any comments on this is appreciated and please feel free to DM me on twitter.

## The initial idea

This is what I was actually trying to do. I wanted to setup an incoming mail server(as this is an initial quick idea, no security elements will be involved in this blog) and I wanted to catch-all the emails that are coming to my email server, get the payload and hit an API endpoint where I can do some further secret magic. 

## The requirements

There's always a recipe to any magic that has been established in the history. Coming to our magical idea, we need the following items.

* Domain name. (Mine is slooshers.xyz, found it cheap, bought it quick)
* A very small instance. (Be it aws or digitalocean or anything that you love. I am currently going with digitalocean because I have some free credits)
* Basic understanding of linux
* Patience

## Configuring the domain

Assuming, by now, you have bought your domain and also have your instance started. So, now we have two elements to configure in the domain's DNS.

1. MX Record
2. A Record

As per wiki, A mail exchanger **record** (**MX record**) specifies the mail server responsible for accepting email messages on behalf of a domain name. It is a resource **record** in the Domain Name System (DNS).

Here's my configuration of the MX record. Now, in my case, my MX record points to a subdomain "mailer" of the domain "slooshers.xyz"

![](/assets/images/screenshot-2020-05-28-at-9-56-48-pm.png)

So, let us go and configure the A record for mailer and point that A record to our instance's public IP.

Inorder to go find your public IP, you need to roam around your instance dashboard a little. In Digital ocean, it is always on the droplet screen, like below.![](/assets/images/screenshot-2020-05-28-at-9-58-31-pm.png)

The ipv4 is my slooshers.xyz IP

So, let's go configure the A record in few seconds.

![](/assets/images/screenshot-2020-05-28-at-10-00-07-pm.png)

Boom! Done. This might take like 2-5minutes for the DNS to properly reflect the changes. But that is all. You can maybe have a quick walk to your balcony and come back.

## Testing the configuration