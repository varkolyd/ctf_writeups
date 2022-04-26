# Chocolate Factory themed CTF writeup

Chocolate Factory themed CTF

First scan the machine with nmap:
nmap -A -T5 -v **{ip}**

![nmap1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/nmap1.png)

![nmap1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/nmap2.png)  

The open ports:

- 21 - ftp
- 22 - ssh
- 80 - http 
- 100, 106, 109, 110, 111, 113, 119, 125 - these TCP ports display the same banner.
