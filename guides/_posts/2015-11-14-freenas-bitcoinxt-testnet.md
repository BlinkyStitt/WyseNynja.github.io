---
category: guides
date: 2015-11-14 22:37:33
layout: page
permalink: guides/freenas-bitcoinxt-testnet
redirect_from: "/posts/2015/11/14/freenas-bitcoinxt-testnet.html"
tags: bitcoin
title: "Running BitcoinXT inside a FreeNAS Jail"
---

I've been running a [Bitcoin Core][bitcoin_core] full node in a [FreeNAS 9.3][freenas] [jail][freenas_jails] for a while now. But due to the politicing happening around Bitcoin Core, I've decided to run [BitcoinXT][bitcoinxt] instead. Before jumping all the way over, I want to run XT v0.11 on the [testnet][testnet].

[jtoomim][jtoomim] is running tests with XT and [provided steps for how you can help him][reddit]. I tweaked the steps a bit to get them working with FreeNAS.

The steps assume you have a working installation of FreeNAS 9.3 running SSHd and that you know how to use [tmux][tmux].

1\. Login to the FreeNAS Web UI and create a standard jail named "testnet"

 * If your network supports it, click "Advanced Mode" and check "IPv6 Autoconfigure"

2\. Open TCP port 18333 on your firewall and forward it to the testnet jail.

3\. Make sure the system clock is accurate:

{% highlight bash %}
ssh freenas
sudo -i

ntpq -p
{% endhighlight %}

4\. Install the following packages in the jail:

{% highlight bash %}
tmux
warden chroot testnet
pkg update -f
pkg upgrade
pkg install ca_root_nss ccache autotools pkgconf gmake boost-libs openssl db48 git
{% endhighlight %}

5\. Make sure the installed packages have no known vulnerabilities:

{% highlight bash %}
pkg audit -F
{% endhighlight %}

6\. Create a user in the jail to run bitcoind:

{% highlight bash %}
adduser
    Username: bitcoin
    Full name:
    Uid (Leave empty for default):
    Login group [bitcoin]:
    Login group is bitcoin. Invite bitcoin into other groups? []:
    Login class [default]:
    Shell (sh csh tcsh git-shell bash rbash nologin) [sh]: bash
    Home directory [/home/bitcoin]:
    Home directory permissions (Leave empty for default):
    Use password-based authentication? [yes]: no
    Lock out the account after creation? [no]:
    Username   : bitcoin
    Password   : <disabled>
    Full Name  :
    Uid        : 1001
    Class      :
    Groups     : bitcoin
    Home       : /home/bitcoin
    Home Mode  :
    Shell      : /usr/local/bin/bash
    Locked     : no
    OK? (yes/no): yes
    adduser: INFO: Successfully added (bitcoin) to the user database.
    Add another user? (yes/no): no
    Goodbye!
{% endhighlight %}

7\. Login as the bitcoin user and build bitcoind:

{% highlight bash %}
su -l bitcoin

echo "export PATH=/usr/local/libexec/ccache:\$PATH" >> ~/.bash_profile
echo "export CCACHE_PATH=/usr/bin:/usr/local/bin" >> ~/.bash_profile

. ~/.bash_profile

git clone https://github.com/jtoomim/bitcoinxt.git -b fortestnet

cd bitcoinxt

export CC=clang
export CXX=clang++
export CXXFLAGS="-I/usr/local/include -I/usr/local/include/db48"
export LDFLAGS="-L/usr/local/lib -L/usr/local/lib/db48"

./autogen.sh
./configure --with-cli --with-gui=no --without-miniupnpc
gmake -j 4
gmake check && echo ":D" || echo "D:"
{% endhighlight %}

8\. If the check succeeded, you will see a happy face and should continue. Otherwise, something has gone wrong and you should investigate gmake's output.

9\. Configure bitcoind, and move what you just built onto your PATH:

{% highlight bash %}
mkdir -p ~/.bitcoin/testnet3
chmod 700 ~/.bitcoin
touch ~/.bitcoin/bitcoin.conf
touch ~/.bitcoin/testnet3/debug.log
chmod 600 ~/.bitcoin/bitcoin.conf
echo "daemon=1" >> ~/.bitcoin/bitcoin.conf
echo "debug=bench" >> ~/.bitcoin/bitcoin.conf
echo "debug=net" >> ~/.bitcoin/bitcoin.conf
echo "logips=1" >> ~/.bitcoin/bitcoin.conf
echo "maxoutbound=8" >> ~/.bitcoin/bitcoin.conf
echo "minrelaytxfee=0.00000001" >> ~/.bitcoin/bitcoin.conf
echo "rpcpassword=$(< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c${1:-32})" >> ~/.bitcoin/bitcoin.conf
echo "rpcuser=bitcoinrpc" >> ~/.bitcoin/bitcoin.conf
echo "testnet=1" >> ~/.bitcoin/bitcoin.conf
echo "use-thin-blocks=0" >> ~/.bitcoin/bitcoin.conf

mkdir ~/bin
mv ~/bitcoinxt/src/bitcoind ~/bin
mv ~/bitcoinxt/src/bitcoin-cli ~/bin
mv ~/bitcoinxt/src/bitcoin-tx ~/bin
{% endhighlight %}

10\. In a new tmux window, submit bitcoind's logs for processing:

{% highlight bash %}
warden chroot testnet
su -l bitcoin
tail -f ~/.bitcoin/testnet3/debug.log | nc bitcoin.dragon.zone 9000
{% endhighlight %}

11\. Return to the first tmux window, start bitcoind, and read the logs:

{% highlight bash %}
bitcoind
tail -n 100 ~/.bitcoin/testnet3/debug.log
{% endhighlight %}

12\. Wait a few minutes for peers to connect to you and for blocks to download.

13\. Check your node for any XT peers:

{% highlight bash %}
bitcoin-cli getpeerinfo | grep subver
{% endhighlight %}

14\. Compare your node's block count with [http://ml.toom.im/](http://ml.toom.im/):

{% highlight bash %}
bitcoin-cli getblockcount
{% endhighlight %}

15\. You did it!


[bitcoin_core]: https://github.com/bitcoin/bitcoin
[bitcoinxt]: https://bitcoinxt.software/
[freenas]: http://www.freenas.org/
[freenas_jails]: https://doc.freenas.org/9.3/freenas_jails.html
[jtoomim]: http://github.com/jtoomim
[reddit]: https://www.reddit.com/r/bitcoinxt/comments/3sq2r3/testnet_is_forking_and_unforking/cwzzhm3
[testnet]: https://en.bitcoin.it/wiki/Testnet
[tmux]: https://tmux.github.io/
