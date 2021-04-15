# fail2ban-ssh-honeypot
Fail2ban jail for creating port 22 (SSH) honeypot

Tired of endless ssh bruteforce attacks ? Even if you are using a certificate or have disabled ssh access completely it will catch a whole lot of compromised IP's and consequently stop some other attack vectors on other ports and services. Port 22 is never missed by a port scan either so you might catch some of these too.

**Installation**

1) If you have SSH access, set the service to listen to some port other than 22, any high number, non reserved port will do.
2) create iptable chain and add rules:

```
iptables -N HONEYPOT
iptables -A HONEYPOT -j LOG --log-prefix "honeypot: " --log-level 6
iptables -A HONEYPOT -j DROP
iptables -A INPUT -p tcp -m tcp --dport 22 --tcp-flags FIN,SYN,RST,ACK SYN -j HONEYPOT

# Save iptables rules
apt update
apt install iptables-persistent
netfilter-persistent save
```
3) add this code block to your jail.local
```
[honeypot]
enabled  = true
filter   = honeypot
logpath  = /var/log/messages
banaction = iptables-allports
bantime  = 604800
maxretry = 1
```
4) copy honeypot.conf to /etc/fail2ban/filter.d/honeypot.conf
5) run fail2ban-client reload honeypot

**Warning**

Don't lock yourself out od your box ! Be aware to connect to the new port from now on. Have a backup plan, lower the bantime, raise the failure attempts (maxretry), whitelist yourself etc...
