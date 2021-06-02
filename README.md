# Docker VPN Settings

## Settings

1. Use the attached `docker-compose.yaml` to build things.
2. Set firewall to allow passthrough of port `1194/udp`.
3. Configure automatic updates to docker images.

## Containers

We have two needs for this, a secure tunnel and a dynamic dns updater.

### [openvpn-as](https://hub.docker.com/r/linuxserver/openvpn-as)

I'm currently using this official OpenVPN tunnel, but I dislike that it limits to two users and it won't be that hard to set up my own using a debian based build.

1. Follow the instructions to wipe the default admin.
2. Follow these [instructions](https://github.com/linuxserver/docker-openvpn-as/issues/96) to set up for local access.
   > In the web gui, only change the hostname under network settings and add your subnet to routing (with nat) under vpn settings. Don't change anything else. Definitely don't change dynamic ip address network
3. Create users with passwords, log in as users to download `.ovpn` profiles.
4. Use `.ovpn` profiles in whatever client app you want.

### [ddclient](https://hub.docker.com/r/linuxserver/ddclient)

This can handle basically any ddns provider. I started with duckdns but gtet weird routing issues on things, no-ip works better.

Note, the `use=` key needs to be defined BEFORE the rest of the keys.

#### duckdns

```ini
##
## Duckdns (http://www.duckdns.org/)
##
use=web					# via web

protocol=duckdns, \
password=<YOUR-TOKEN-HERE> \
<YOURDOMAIN> # eg domain, not domain.duckdns.org
```

#### no-ip

To be tested using these [instructions](https://coderwall.com/p/wux7ug/ddclient-no-ip).

**TEST SUCCESS**

```ini
##
## No-IP (http://my.noip.com/)
##
use=web, web=checkip.dyndns.com/, web-skip='IP Address'

protocol=dyndns2
server=dynupdate.no-ip.com
login=<YOURUSERNAME>
password=<YOURPASSWORD>
your_domain.com
```

## Future Plans

I really dislike the limitations of OpenVPN-AS, so I'll probably just grab the community build of `openvpn` and roll my own container. Settings should be pretty easy to script up. We'll see how lazy I remain. Could be a fun use of Jenkins to do regular rebuilds...
