# Create a transparent proxy

## Scenario #1: Debian and Windows, no VPN

Enable IP forwarding

    nano /etc/sysctl.conf

Comment out the following lines:

    sysctl -w net.ipv4.ip_forward=1
    sysctl -w net.ipv6.conf.all.forwarding=1
    sysctl -w net.ipv4.conf.all.send_redirects=0

Create iptables ruleset to forward traffic to mitmproxy

    apt install iptables-persistent

Replace **ens160** with the name of your network adapter!

    /sbin/iptables -t nat -A PREROUTING -i ens160 -p tcp --dport 80 -j REDIRECT --to-port 8080
    /sbin/iptables -t nat -A PREROUTING -i ens160 -p tcp --dport 443 -j REDIRECT --to-port 8080
    /sbin/ip6tables -t nat -A PREROUTING -i ens160 -p tcp --dport 80 -j REDIRECT --to-port 8080
    /sbin/ip6tables -t nat -A PREROUTING -i ens160 -p tcp --dport 443 -j REDIRECT --to-port 8080

Make changes permanent by saving the rules

    /sbin/iptables-save > /etc/iptables/rules.v4
    /sbin/ip6tables-save > /etc/iptables/rules.v6

Run mitmproxy

    mitmproxy --mode transparent --showhost

## Scenario #2: Debian x2 and Windows with VPN

