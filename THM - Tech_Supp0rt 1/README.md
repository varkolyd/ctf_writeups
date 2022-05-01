#  Tech_Supp0rt 1 CTF writeup | Tryhackme.com

Hack a scammer's website and ruin their day.

First scan the ports with rustscan:\
![rsc1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/rustscan_1.png)\

![rsc1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/rustscan_2.png)

Ports:\
Open 10.10.197.14:22 - SSH\
Open 10.10.197.14:80 - HTTP\
Open 10.10.197.14:139 - NetBIOS\
Open 10.10.197.14:445 - SMB\

Now let's inspect this IP in our browser.

![indexhtml](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/indexhtml.png)

It is a basic apache webserver, but there's probably more to it. Find it out with gobuster!

![gobuster](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/gobuster.png)

*/test* and */wordpress* are the directories we are looking for here so let's check both of them.

![test](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/test.png)

Test is basically a static scammer site, can't really click anywhere. I've looked into the source code but besides the images not much seems to be here.
Let's try wordpress maybe we will find more useful stuff there.

![wp](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/wordpress.png)
