# PasswordAudit-NICE

Pretty Safe Electronics  AD and Webapp password audit
[NICE portal preview[(https://portal.nice-challenge.com/curator/challenges?work_role=&specialty_area=23&environment=2&os=&category=&challenge_type=&ksa_id=&ksa_desc=&task_id=&task_desc=&ku_desc=&title=&scenario=&submit=Search#68/1)

# Lab #1: Notes for Windows (AD) process

#### ON the security box  (playerone/password123)
```
find / -name mimikatz
cd /usr/share/windows-resources/mimikatz
# control panel / system and security / system to determine x64/i86
scp /usr/share/windows-resources/mimikatz/x64/* playone@172.16.30.55:.
```
#### ON domain controller
```
.\mimikatz.exe
log a.log
lsadump::dcsync /domain:prettysafeelectronics.io /all /csv
exit
```

#### Back on the security box
```
scp playerone@172.16.30.55:a.log .
# edit down to just the hashes
cut -f 3 a.log | tail -n +9 | head -n -3 > hashes
/usr/sbin/john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hashes
# Two passwords 98765421 & iloveme
more .john/john.pot
# match hashes in a.log  user = nkeefe & jcortes
```

#### back on domain controller
Admin tools / active directory users and computers
set users to force password change on next login

# Lab #2: Notes for Webapp (Joomla) process

#### ON the security box  (playerone/password123)
WORK FROM Security Desk for scrolling terminal window cut/paste

```
ssh playerone@172.16.10.100
find / -name configuration.php 2>/dev/null
cd /var/www/html
more configuration.php # to find user/password/db/dbprefix
mysql -u pse -p -h 172.16.30.88
use prettysafe
show tables;
show columns in pretty_users;
select id,password from pretty_users;
```
cut/paste hashes to text file --> b.log

#### On security desk
```
cut -d ' ' -f 4 b.log > joomla_hashes
/usr/sbin/john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt joomla_hashes
OR
hashcat -m 0 --force joomla_hashes /usr/share/wordlists/rockyou.txt
hashcat --show joomla_hashes
# match 1234567890 & Ashley to hashes; users = 
grep 'adff\|e807' b.log
```

#### Reconnect to MYSQL
```
update pretty_users set requireReset=1 where id=734 or id=740;
```

