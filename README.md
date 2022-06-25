# Docker VPN Settings

## Setup

1. Rename `.env.exmaple` to `.env`
2. Edit the variables as you see fit.

## Settings

1. Use the attached `docker-compose.yaml` to build things.
2. Set firewall to allow passthrough of port `51820/udp`.
3. Configure automatic updates to docker images.

## Containers

We have two needs for this, a secure tunnel and a dynamic dns updater.

### [ddclient](https://hub.docker.com/r/linuxserver/ddclient)

This can handle basically any ddns provider. I started with duckdns but got weird routing issues on things, no-ip works better.

Note, the `use=` key needs to be defined BEFORE the rest of the keys.

#### duckdns

**TEST FAILURE**

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
<YOURFQDN> # the ddns fqdn, eg. something.noip.org
```

## Wireguard

https://hub.docker.com/r/linuxserver/wireguard

Had to install a couple things and reboot. Follow the steps here for your OS: https://www.wireguard.com/install/

```bash
sudo yum install kmod-wireguard wireguard-tools
```

Also had to add portforwarding on my router as described above for `51820/UDP`.

After that, I can just change the number of clients to generate. I think I can provide a list of names as well.
It'll spit out some QR codes that I just need to scan to add to the [phone app](https://play.google.com/store/apps/details?id=com.wireguard.android&hl=en_US&gl=US). The QR codes are located in under the docker configs folder for this container, I've got them going under a folder called `wireguard`.

I also found this other container:

- https://old.reddit.com/r/selfhosted/comments/ug5vz8/setting_up_a_vpn_for_homelab/
