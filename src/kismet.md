# Chapter 11 - Kismet


## Setup
```
sudo apt install kismet

ls -al /etc/kismet/

sudo mkdir /var/log/kismet

```

## Start
```
cd ~
sudo kismet -c wlan0 --no-ncurses
KISMET - Point your browser to http://localhost:2501 (or the address of this system) 

^C

ls -al /var/log/kismet/

sudo kismet -c wlan0:channels="4,5,6"
^C

sudo kismet --daemonize


```

## Remote Capture
```
sudo kismet

ssh kali@192.168.62.192 -L 8000:localhost:3501

sudo kismet_cap_linux_wifi --connect 127.0.0.1:8000 --source=wlan0

sudo kismet_cap_linux_wifi --connect 127.0.0.1:8000 --source=wlan0
^C


ssh kali@192.168.62.192 -L 8000:localhost:3501
exit

^C

```

## Log Files
```
sudo sqlite3 /var/log/kismet/Kismet-20200917-18-45-34-1.kismet
.tables
.schema devices


.headers on
select type, devmac from devices;

.quit

sudo sqlite3 /var/log/kismet/Kismet-20200917-18-45-34-1.kismet "select type, devmac from devices;"


sudo kismet -c Documents/Network_Join_Nokia_Mobile.pcap:realtime=true
^C

```

## Exporting Data
```
kismetdb_to_pcap --in Kismet-20200917-18-45-34-1.kismet --list-datasources

kismetdb_to_pcap --in Kismet-20200917-18-45-34-1.kismet --out sample.pcapng --verbose


kismetdb_dump_devices --in /var/log/kismet/Kismet-20200917-17-45-17-1.kismet --out sample.json --skip-clean --verbose


```