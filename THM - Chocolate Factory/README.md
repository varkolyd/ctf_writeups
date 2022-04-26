# Chocolate Factory themed CTF writeup

Chocolate Factory themed CTF

First scan the machine with nmap:
nmap -A -T5 -v <**ip**>

![nmap1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/nmap1.png)

![nmap1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/nmap2.png)  

The open ports:

- 21 - ftp
- 22 - ssh
- 80 - http 
- 100, 106, 109, 110, 111, 113, 119, 125 - these TCP ports display the same banner.

Anonymous FTP login is allowed so let’s try that and see what we find

![ftp](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/ftp.png)

Download gum_room.jpg to our machine. Maybe there is something hidden behind this picture. 
We can find it out with steghide.

![steghide](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/steghide.png)

Use: steghide extract -sf <**file**>
