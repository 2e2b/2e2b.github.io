Hello! In this post, I will document my solutions to the levels of (Overthewire's bandit)[https://overthewire.org/wargames/bandit/].
---
Level 0: goal is to log in to bandit's server using ssh using port 2220. The password was provided, so all I had to do was write the command.
> ssh bandit0@bandit.labs.overthewire.org -p 2220

password: bandit0

---
Level 1: The password was stored in a file called "readme" in the home directory, so I did the following
> cat readme

and got the password (ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If])

---

Level 2: This level added a tad bit of complexity as it required me to open a file who's name was "-", which makes it so "cat -" doesn't work. Instead, a little digging found me the correct command
> cat ./-

which got me the password (263JGJPfgU6LtdEvgfWU1XP5yac29mFx)

---

Level 3: This level needed me to get the password out of a file called "spaces in this filename", which you also can't just cat directly. The solution I used was to wrap the name in quotes, 
> cat "spaces in this filename"

to get the password (MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx)

---

Level 4: This level needed me to get the password out of a hidden file, which was located inside the inhere directory. First, I used
> cd inhere

to get into the directory. Then I used 
> ls -a

to get all of the files inside, including hidden ones. This showed me a file called "...Hiding-From-You", so I could use 
> cat ...Hiding-From-You

to get the password (2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ)

---

Level 5: In this level, the inhere directory has 10 files in it, and the password is stored in the only readable one. I initially tried 
> file *

to get the file type of each file, but it didn't like the dashes in front of each file name. Some more researching about the syntax for the file command showed me that I needed to do 
> file -- *

to get the file type of each file. In this case, it showed me that -file07 was the one that contained readable text, which was the password (4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw)

---

Level 6: This time, the password was stored somewhere in the large number of directories present and had the attributes that the file was 1033 bytes in size, was human-readable, and was non-executable. the find command doesn't have a human-readable flag, but it does have flags for size and for executable!
> find -size 1033c ! -executable

found the file (./inhere/maybehere07/.file2). This file contained the password (HWasnPhtq9AVKe0dmk45nxy20cvUa6EG) and a bunch of white space pumping it up to 1033 bytes.

---

Level 7: This level required another specialized use of the find command. The file was stored somewhere on the server, was owned by user bandit7, group bandit6, and was 33 bytes in size. To put these specifications into a find command, I first went to root with cd /, then did
> find -user bandit7 -group bandit6 -size 33c

This still spat out quite a few files, but I wanted to check and see if any of them were called "password" first. I extended my previous command to 
> find -user bandit7 -group bandit6 -size 33c | grep "password"

and it found a file called ./var/lib/dpkg/info/bandit7.password . Bingo!
The password was morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj.

---

Level 8: This level contained a massive text file called data.txt and the password was next to the word "millionth". This called for use of the grep command to find the line with the word millionth, because the password should have been stored next to it. 

> grep "millionth" data.txt

showed me the password (dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc)

---

Level 9: This level contains another massive file called data.txt and the password is the only unique line in the file. This would call for use of the uniq command, but uniq can only detect duplicate lines that are right next to each other, so the sort command is also necessary. 

>sort data.txt | uniq -u

This command passes the sorted data list into uniq -u, which discards all duplicates. This left behind only one line, the password (4CKMh1JI91bUIZZPXDqGanal4xvAg0JM)!

---

Level 10: The password is stored somewhere in data.txt, which is mostly unprintable characters (garbage data) and is perceded by a few = signs. To clean up all of the unreadable data, I used the strings command. This command throws away all unprintable characters, leaving behind only printable ones. I then passed the output of that into the grep command to find "===".

>strings data.txt | grep "==="

This spat out the password (FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey)

---

Level 11: The password is stored in data.txt, which is encoded in base64. To decode base64, I used 
> base64 -d data.txt

which gave "The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr"

---

Level 12: The password is stored in data.txt, where all lowercase and uppercase letters have been rotated 13 positions from normal. To decode this, I used the tr command, which can be used to replace characters with different characters in the same string (give it 2 arrays of characters and it will replace each instance of a character in array 1 with the corresponding array 2 character)

> cat data.txt | tr "A-Za-z" "N-ZA-Mn-za-m"

Outputs "The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4"

--- 

Level 13: The data.txt file in this level is a hexdump of the password which has been repeatedly compressed using different compression algorithms. To solve this, I made a temp directory and then used xxd -r data.txt to get the original file (un-hexdump it). From there, I had to unzip it like 9 times using bunzip2 (for bzip2 archive), gunzip (for zip.gz) archive, and tar -x (for tar archive). I also used the file command to identify what files were inside, as they often had the wrong extensions. After a lot of unzipping, I got a text file saying "The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn"

---

Level 14: This level contained an ssh private key that could be used to log in to the next level. I copied it out of terminal using cat sshkey.private and pasted it into a file called sshkey.private on my machine. To log in to the next level, I used 

>ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220

This used the ssh key to authenticate my login, allowing me to access the password, which the level said was at /etc/bandit_pass/bandit14. The password was MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS.

---

Level 15: This level wanted me to (from level 14) submit the password I just got to localhost on port 30000. I did 
> telnet 127.0.0.1 30000

to connect, and then pasted in the password from earlier. I then got the message "Correct!" followed by the next password (8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo).

---

Level 16: For this level, I am supposed to submit the password to the current level over a ssl connection. To figure this out, I spent some time reading about openssl and ncat until I find openssl s_client, which is a command that allows you to make a connection over ssl. I used the connect flag, as that's what we're trying to do. 127.0.0.1:30001 is the localhost port the level wanted
> openssl s_client -connect 127.0.0.1:30001

and then pasted in the password and sent it. I got back the next password, kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx.

---

Level 17: For this level, I needed to find an ssl server on a port somewhere between 31000 and 32000 that would give me the right password if I sent it the current one. For this task, I immediately thought of nmap, and did a bit of research to figure out how to use it to find ssl ports. The -sV flag was what I was looking for. This quickly pointed me to port 31790 as the correct port. However, there was another issue. Apparently, openssl doesn't like it when your submission begins with a "k" (treats it as a key update rather then sending data). Overthewire mentioned the fix for this was located in the man page for openssl s_client, and it turns out the ign_eof flag was what I wanted.

>openssl s_client -ign_eof -connect 127.0.0.1:31790 -ign_eof

The server gave me a long RSA private key that I would use to log in to the next level. 

---

Level 18: This level is pretty easy. It gives me 2 lists of passwords and tells me that they are the same except for one particular line. That different line in passwords.new is the correct password. To solve this, I simply did 
> diff passwords.old passwords.new

which said that line 42 was the only different one. Line 42 in passwords.new is x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO, which is the correct password!

---

Level 19: Whenever you try to log in, you will immediately be logged out due to a bad .bashrc configuration. As such, we need to utilize another function of ssh: running commands directly. After all the normal arguments, you can put any command and ssh will run it on the target machine, bypassing the instant logout. To do this, I did 
> ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme

This read the contents of the readme file, which contained the password (cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8).

---

Level 20: To complete this level, I needed to use to bandit20-do executable file to run commands as the user bandit20. The path to the password file was /etc/bandit_pass/bandit20, so I ran the following in order to use bandit20-do
> ./bandit20-do cat /etc/bandit_pass/bandit20

which gave me the next password (0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO)

---

Level 21: This level was very confusing. It required us to use another executable binary which, when used, would connect to localhost on some port and then listen for incoming data sent to it. If that data contained the current level's password, it would output the next level's password. To do this, I started researching potential ways to host something in such a way it would send the data when something connected to it, which eventually (after about 15 minutes of reading man pages) lead me to the nc command. nc (short for netcat) could listen on TCP ports, which lead me on to the fact that it was the command I needed. The -l flag would allow it to listen for connections, and the -p let me specify a port. Now I had all the tools to solve the problem, but I still needed to figure out how nc worked. Here is the command I eventually came up with
> echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -l -p 5000

This command passed the text phrase (the password) into nc, and had it serve that on port 5000. Now to feed that password into suconnect, I did
> ./suconnect 5000

This allowed it to read from the port, and it gave me the next password! (EeoULMCra2q0dSkYj561DX7s1CpBuOBt)

---

Level 22: 

This level asked us to investigate a cron job that was running on the server. The bandit level's description pointed me towards a seemingly malfunctioning cron job that was running the following command

>/usr/bin/cronjob_bandit22.sh &> /dev/null

/dev/null is nowhere, so whatever the output of cronjob_bandit22.sh isn't somewhere we can read. Not yet, at least. I hopped on over to see what commands were inside cronjob_bandit22.sh and found that it was running this command:

>cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

Which means that the password for bandit22 was getting put into /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv. Another hop over there found me the t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv file, which contained the password (tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q)

---

Level 23: 
