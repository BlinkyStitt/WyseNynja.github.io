---
category: guides
layout: page
permalink: guides/ansible-managed-freenas-jails
tags: ansible, freenas
title: "Creating an Ansible-managed FreeNAS Jail"
---

I use [plugins][freenas_plugins] for a few things on my [FreeNAS][[freenas], but sometimes I need more flexibility.

1\. Login to the FreeNAS Web UI and create a standard jail.

 * In these steps, the jail is named "example". Replace "example" with whatever you chose.

 * To keep things simple, don't use dashes or underscores in the name.

 * If your network supports it, click "Advanced Mode" and check "IPv6 Autoconfigure"

2\. Copy the bootstrap script to the jail and run it:

{% highlight bash %}
scp mgmt:/usr/local/etc/ansible/bootstrap.sh .
cat bootstrap.sh

scp bootstrap.sh freenas:~
ssh freenas
sudo chown root:wheel ~/bootstrap.sh
sudo mv ~/bootstrap.sh /mnt/storage/jails/example/root/
sudo su -l
tmux
warden chroot example
sh /root/bootstrap.sh && rm /root/bootstrap.sh || echo "D:"
{% endhighlight %}

3\. Add A (and AAAA if you enabled IPv6) records to your DNS server.

4\. Open any necessary ports on your firewall and forward them to the jail.

5\. Add the jail to your ansible hosts file and setup whatever else in ansible you need.

6\. Run ansible on the new jail:

{% highlight bash %}
ssh mgmt
./run.sh --limit example
{% endhighlight %}

7\. You did it!


[freenas]: http://www.freenas.org/
[freenas_plugins]: https://doc.freenas.org/9.3/freenas_plugins.html
