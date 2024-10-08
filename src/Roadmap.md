# Chapter 0 - Roadmap

## Quick Notes

```
sudo lsusb -vv
lsusb
sudo airmon-ng

Bring normal network back up
------
sudo service networking restart
sudo service NetworkManager restart

or

service networking start
service network-manager start


-----
Update Kali:

sudo apt update 
sudo apt full-upgrade -y

------

Update a local git repo:

either:    git pull origin master

    or:    git pull origin main

-----

For report building:
https://github.com/noraj/OSCP-Exam-Report-Template-Markdown

Creates the markdown file:
ruby osert.rb init

Build the pdf:
ruby osert.rb generate

```
## Exam Roadmap
```
ssh <username>@<target_ip> -p<port>                        ## connecting to the target system with SSH information provided by @offsec
iw dev wlan0 scan | grep SSID                              ## wireless networks within the range of the wireless card (wlan0) are detected
airmon-ng start wlan0                                      ## setting the wireless card (wlan0) to monitor mode
airodump-ng mon0                                           ## detecting surrounding networks by listening to the network in monitor mode
iwconfig mon0 channel 3                                    ## change the channel where the wireless network card is located
iwlist mon0 channel                                        ## view the channel where the wireless network card is located
airodump-ng -c 3 --bssid <AP_MAC> -w <capture_file> mon0   ## listening to the target AP on the specified channel
                                                                              
       
 
----------------------------------------------------------------------------------------------------------------------------------------------------------------
```

## Opening more than one screen with the "screen" tool;
```

  # screen -S test        ## creating a workspace
  # scren -l              ## list actively used screens
  # screen -a -r 13245    ## switch to a specifically selected screen
  # CTRL + a + n          ## connects to the next session
  # CTRL + a + p          ## go back to the previous session



----------------------------------------------------------------------------------------------------------------------------------------------------------------



```

### Attack Map - WEP?###
```
> WEP;

  AUTH: OPN? or SKA?

  > AUTH: OPN;

    Any client connected to AP? YES? or NO?

    > YES;

      # ARP Request Replay Attack
      # Interactive Packet Replay Attack
      # Deauthentication Attack (can be used in both cases (yes or no))
  
    > NO;

      # Fake Authentication Attack (can be used in both cases (yes or no))
      # Fragmentation Attack
      # Korek ChopChop Attack


  > AUTH: SKA (Bypassing WEP Shared Key Authentication);

    ## There is a client connected to the AP. You can follow the steps below to attack;

     # Deauthentication Attack
     # Fake Shared Key Authentication Attack
     # ARP Request Replay Attack
     # Deauthentication Attack
     # Aircrack-ng


```

### Attack Map - WPA/WPA2?###
```
> WPA/WPA2

  ## Attack;

    # Deauthentication Attack
  
  ## Cracking the network key;

    # with Aircrack-ng
    # with JTR and Aircrack-ng
    # with coWPAtty
    # with Pyrit



```