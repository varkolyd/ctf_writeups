#  Tech_Supp0rt 1 CTF writeup | Tryhackme.com

Hack a scammer's website and ruin their day.

First scan the ports with rustscan:


![rsc1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/rustscan_1.png)


![rsc1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/rustscan_2.png)

Ports:\
Open 10.10.197.14:22 - SSH\
Open 10.10.197.14:80 - HTTP\
Open 10.10.197.14:139 - NetBIOS\
Open 10.10.197.14:445 - SMB

Now let's inspect this IP in our browser.

![indexhtml](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/indexhtml.png)

It is a basic apache webserver welcome site, but there's probably more hidden underneath. Find it out with gobuster!

![gobuster](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/gobuster.png)

*/test* and */wordpress* are the directories we are looking for here so let's check both of them.

**TEST**\
![test](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/test.png)

Test is basically a static scammer site, can't really click anywhere. I've looked into the source code but besides the images not much seems to be here.
Let's try wordpress maybe we will find more useful stuff there.

**WORDPRESS**\
![wp](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/wordpress.png)

Okay so here we could browse all the tabs or be lazy and run gobuster. I did both.

By browsing this directory I found a wordpress admin login site and some Hello World comments by the user named: **support**

Gobuster basically confirmed this

`gobuster dir -u http://10.10.197.14/wordpress -w /usr/share/dirb/wordlists/common.txt -x php,html,txt,sh`

    /license.txt          (Status: 200) [Size: 19915]
    
    /readme.html          (Status: 200) [Size: 7345]
    
    /wp-admin             (Status: 301) [Size: 325] [--> http://10.10.197.14/wordpress/wp-admin/]
    
    /wp-includes          (Status: 301)[Size:328][-->http://10.10.197.14/wordpress/wp-includes/]

I did the same with /test but haven’t found anything useful there

At this point we hit a dead end, so our only remaining option is to check SMB shares and find something (hopefully).

![smb1](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/smbclient.png)

![smb2](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/smb2.png)

Success! The first share didn't work but websvr had this interesting enter.txt.\
Cat this out.

`cat enter.txt`

    GOALS

    =====

    1)Make fake popup and host it online on Digital Ocean server
    2)Fix subrion site, /subrion doesn't work, edit from panel
    3)Edit wordpress website



    IMP
    ===
    Subrion creds
    |->admin:7sKvntXdPEJaxazce9PXi24zaFrLiKWCk [cooked with magical formula]
    Wordpress creds
    |->

Cooked with magical formula suggests that we are dealing with a hashed pw here. First I tried crackstation and hashanalyzer but both attempts failed.\
Cyberchef on the other hand could work perfectly with.. the magic formula.

![chef](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/cyberchef.png)

That was easy, we found a password: **Scam2021**

The note left by the scammer will lead us to our next step if we take it literally. Let's navigate to */subrion/panel*

![subrion](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Tech_Supp0rt%201/tech_supp0rt%201%20images/subrion%20panel.png)

It is worth what version the CMS has. Probably it would be a good idea to search any vulnerabilities (if there are any).\ We found just what we needed:
https://www.exploit-db.com/exploits/49876




##### author: Dániel Varkoly
