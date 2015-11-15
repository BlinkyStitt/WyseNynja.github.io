= new freenas jail

warden chroot NAME
pkg update -f
pkg upgrade
pkg audit -F

== maybe enable sshd

vi /etc/rc.local

    sshd_enable="YES"

service sshd start

install the ansible mgmt key for the root user

== install what we care about

pkg install chocolatedoom

