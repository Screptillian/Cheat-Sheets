# Can run fail2ban with sudo and write to fail2ban actions.d

```sed "s/<iptables> -I f2b-<name> -s <ip> -j <blocktype>/chmod u+x \/bin\/bash/g" /etc/fail2ban/action.d/iptables-multiport.conf > config.conf```

```rm -f /etc/fail2ban/action.d/iptables-multiport.conf```

```mv config.conf /etc/fail2ban/action.d/iptables-multiport.conf```

```sudo /etc/init.d/fail2ban restart```

```bash -p```
