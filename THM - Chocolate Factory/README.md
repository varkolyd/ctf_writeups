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

Use: steghide extract -sf <**file**>

![steghide](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/steghide.png)

The txt seems to be a base64 encoded text so let’s decode it in the terminal\
` cat b64.txt | base64 -d `

It seems to be an etc/shadow file and there is a user named charlie\
*charlie:$6$CZJnCPeQWp9/jpNx$khGlFdICJnr8R3JC/jTR2r7DrbFLp8zq8469d3c0.zuKN4se61FObwWGxcHZqO2RJHkkL1jjPYeeGyIJWE82X/:18535:0:99999:7:::*


Let’s find the subdomains using gobuster\
`gobuster dir -u http://<ip>/ -w /usr/share/dirb/wordlists/common.txt -x php,html,sh,txt`

