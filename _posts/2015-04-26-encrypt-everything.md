---
category: posts
layout: post
tags: security
title: "Encrypt Everything"
date: 2015-04-26 22:00:00
---

I wrote the original version of this post a few years ago on a webpage long since abandoned. When I heard about [China's Great Cannon][chinas-great-cannon], it made me want to dig this up and update it, and here we are.

If someone is positioned to manipulate your Internet traffic, they can do nasty things like use your computer as part of a DDOS attack or infect you with malware. Sometimes even encrypted traffic is vulnerable if the cryptography is obsolete or otherwise compromised.

To show my friend how easy this was:

1. I downloaded [snowstorm][snowstorm] to my webserver and copied the minified javascript to /usr/local/www/google-pwnalytics.com/analytics.js
2. I setup my webserver to serve /usr/local/www/google-pwnalytics.com/ for www.google-analytics.com
3. I configured my home router's DNS server to point www.google-analytics.com to my webserver
4. I cleared my browser cache and reloaded this page. It was snowing.

The number of sites that are vulnerable to this is scary. With my [DNS poisoned][dns-spoofing], it snows on any site that uses HTTP analytics.

Manipulating unencrypted traffic has been used to do things much worse than simply making it snow. [China's Great Cannon][chinas-great-cannon] replaced Baidu's unencrypted analytics javascript with code that attacked GitHub and other sites.

A "trusted" page being modified could cause a lot of harm. What if the malicious script grabbed your login cookie? Or waited for you to type your password and sent that to an attacker? Or opened a pop-up to install malware? Or took advantage of one of the many known or unknown [vulnerabilities][cve] in your browser to install malware on your computer without you even clicking anything?

Even worse, an attacker isn't limited to replacing a javascript file like the google analytics javascript. It is trivial to write a proxy server that manipulates a page however the attacker wants and then literally any HTTP page you load could be doing something malicious under the guise of a “trusted” website.

Want to be more scared? Even with encryption, [sophisicated attacks][dyre wolf] can still do a lot of harm.

**tl;dr; encrypt everything always and hope no one is directly targeting you!**

[chinas-great-cannon]: https://citizenlab.org/2015/04/chinas-great-cannon/
[cve]: http://cve.mitre.org/
[dns-spoofing]: http://en.wikipedia.org/wiki/DNS_spoofing
[dyre wolf]: https://portal.sec.ibm.com/mss/html/en_US/support_resources/pdf/Dyre_Wolf_MSS_Threat_Report.pdf
[snowstorm]: http://www.schillmania.com/projects/snowstorm/