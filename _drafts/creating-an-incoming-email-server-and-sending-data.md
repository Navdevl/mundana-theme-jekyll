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
* A very small instance. (Be it aws or digitalocean or anything that you love. I am currently going with digitalocean because I have some free credits) Ubuntu 16.04 is my usual choice.
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

There are few online services to check if you A name and MX record configurations are established properly. I tested mine at [https://mxtoolbox.com/](https://mxtoolbox.com/ "https://mxtoolbox.com/")

## Installing Postfix

As per wiki, **_Postfix_** is a free and open-source mail transfer agent (MTA) that routes and delivers electronic mail. In order to understand the complete workflow of how email works, there are tons of resources out there and we aren't covering them in this post. You can still go refer to these below resources where I found some ideas on the email and how everything happens.

Now, let us roll our sleeves and start our work.

 1. First ssh into your instance. Once you are in, make sure to update it by running `apt-get update` 
 2. Change your hostname value because this will be very much needed in a lot of places. Run `vi /etc/hostname/` and change the value to **mailer** and save it.
 3. Verify it by typing `hostname -f` in the command line to check your FQQN. Mine will display the following  
    _root@mailer:\~# hostname -f_

    _mailer.slooshers.xyz_ 
 4. Then install postfix by running `apt-get install postfix` 
 5. Postfix installation will ask you to go through a wizard workflow to setup.

    ![](/assets/images/screenshot-2020-05-28-at-11-20-07-pm.png)
 6. Choose **Internet Site**
 7. Now, verify if your hostname if properly filled as per the FQDN.
 8. For **Root and postmaster mail recipient,** just for now you can leave this blank
 9. In the other destinations make sure both mailer.yourdomain.com and yourdomain.com exists. Because this is very much needed as per our story line.
10. Other things, you can just go with default.

If any of the above steps didn't occur to you, you can run `dpkg-reconfigure postfix` and reconfigure again. Don't sweat. 

Right now, we are at 50% work done.

## Get the incoming mails in

Most our work is going to revolve around postfix's configuration from now on. You can find the postfix configuration files at `/etc/postfix.`

Preferably cd into that directory for more ease. 

You will find `main.cf` file inside the directory which is going to cover almost what we are going to go with here. So, I am cutting down a lot of unnecessary elements of explanation in here to explain each of the configuration variables inside `main.cf` and going to share the configuration that I am currently using.

    smtpd_banner = $myhostname ESMTP $mail_name $mail_version
    biff = no
    
    append_dot_mydomain = no
    readme_directory = no
    compatibility_level = 2
    
    myhostname = mailer.slooshers.xyz
    myorigin = /etc/mailname
    mydestination = mailer.slooshers.xyz, mailer.slooshers.xyz, slooshers.xyz, localhost.slooshers.xyz, , localhost
    relayhost =
    mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 167.71.230.101
    mailbox_size_limit = 0
    recipient_delimiter = +
    inet_interfaces = all
    inet_protocols = all
    
    alias_maps = hash:/etc/aliases
    alias_database = hash:/etc/aliases
    transport_maps = regexp:/etc/postfix/redirect.regexp
    