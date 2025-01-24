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

This level involved examining another cron job to see what it was doing. This quickly pointed me to the script being executed, which was running the following code: 
> #!/bin/bash
>
>myname=$(whoami)
>mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
>
>echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
>
>cat /etc/bandit_pass/$myname > /tmp/$mytarget

This code was calculating an md5 hash of the string "I am user (username)" and pasting the password it into /tmp/(hash). In order to figure out where the password was, I had to calculate the hash for "I am user bandit23" (in order to log in to bandit23). Luckily, the code was already there for me to use, except I modified it a bit first. 

>echo I am user bandit23 | md5sum | cut -d ' ' -f 1

Which gave me the hash 8ca319486bfbbc3663ea0fbe81326349. From there, all I needed to do was run

> cat /tmp/8ca319486bfbbc3663ea0fbe81326349

and I got the password (0Zf11ioIjMVN551jX3CmStKLYqjk54Ga)

---

Level 24:

In this level, there is a cronjob that goes in and runs all scripts in the folder /var/spool/bandit24/foo as the user bandit24. However, we have write access to this directory. Time to prepare a .sh injection script for this cronjob to run. In my script, I wrote
> cat /etc/bandit_pass/bandit24 > /tmp/tmp.WLTx7GEp9L/password.txt

This way, the script (running as user bandit24, would access its own password and put it somewhere I can see it. 
To put the script where it would get run, I did
> cp script.sh /var/spool/bandit24/foo/script.sh

I also made a password.txt file with

>touch password.txt

However, after 1 minute, the script had been deleted but no password had appeared in password.txt. This was perplexing, but then I realized that bandit24 probably didn't have access to password.txt. To rectify this, I gave it permission with 
> chmod 777 password.txt

I also copied my script in and gave it full access to the script with
> chmod 777 /var/spool/bandit24/foo/script.sh

After another little while, the next password showed up in password.txt! (gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8)

---

Level 25: This level said that there was something listening on port 30002 that would give me the next password if I gave it the current password and a 4-digit pin that had to be bruteforced. Time to write a bash script to try all of them!

Here is the bash script I came up with
>#!/bin/bash
>
>for i in {0..10000};
>do
>echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i" | nc 127.0.0.1 30002 > output.txt;
>done

However, this tried the first pincode and then got stuck, so I needed to rearrange it. My next thought was maybe I could somehow echo all of it into string, and then send it through nc so it would run all of them. While trying to figure out how to write all this, I realized you could pipe the entire output of a for loop into something by using a pipe after the done command. This worked! Since the for loop was spitting out the attempts line by line, I could pipe them into nc as so
>#!/bin/bash
>
>for i in {0..10000};
>do
>echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i";
>done | nc 127.0.0.1 30002

After a lot of wrong attempts, this automated script eventually gave me the next password (iCi86ttT4KSNe1armKiwbQNmB3YJP3q4)

---

Level 26

Inside this level, there was an ssh key for the next user. Because of this, I immediately copy-pasted the key into a file on my machine, then ssh'd into bandit26.
> ssh -i bandit26.sshkey bandit26@bandit.labs.overthewire.org -p 2220
However, this immediately logged me out, and I realized (from the level description) that the user bandit26's path to a shell wasn't the normal one. So, I googled where to find the path to a given user's default shell, and I found from google that the default shell is always the last line of /etc/passwd for a given user. When I opened up /etc/passwd, I found that bandit26's shell was actually located at /usr/bin/showtext, which is not the normal shell. Upon looking at this command, I saw that it was a simple script that sent some text and then exited, instead of spawning a bash shell like we wanted. Somehow, we had to modify this file so it would let us in. Since the /usr/bin/showtext was running the more command on the big text banner, I decided to read the manpage on more to see what it could do. What caught my eye is that it will display text page by page if it wouldn't all fit on screen, and that it had an editor mode that could be accessed by pushing v. if I made my terminal window small enough, maybe the more command would have to split the banner across multiple pages, which would give me a window to open up a text editor and somehow  change the shell to /bin/bash? Time to put this theory to the test. I scrunched my terminal down, ran
> ssh -i bandit26.sshkey bandit26@bandit.labs.overthewire.org -p 2220

and the more window opened up instead of exiting. From there, I pushed v and was brought to vim. I don't really know how to use vim, but I found that you could enter command mode by typing colon, but I didn't know any vim editor commands either. This is why we use google! Turns out you can change the shell from a vim editor by using the set shell command. I typed in
> :set shell=/bin/bash
and it seemed to work. To start the shell, I ran
> :shell
and I was in. Yay! I quickly grabbed bandit26's password (s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ)

---

Level 27

Level 27 picks up right where level 26 left off, and there is an executable binary called bandit27-do that lets you run commands as bandit27. That's convenient! I ran 
> ./bandit27-do cat /etc/bandit_pass/bandit27

and immediately had the password. (upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB)

---

Level 28:
This level asked me to clone a repository using git and to find the password in it. To clone the repo, I used
> git clone git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo

The important bit is the localhost:2220 as that was a slight modification of the url given so I could connect over the correct port.
Once inside the repo, the password was inside a plain text file called README. It was Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN.

---

Level 29:

This level asked me to find the password from another git repo. After cloning it like I did in the previous step, I opened the README.md to check out what was going on. There was no password in there, so I went back to the man page for git as the level said I only needed the git command to solve the level. It was there that I found git log. This command lets you see past versions of a file, which, seeing as the only file in the repository was README.md, seemed promising. I did
> git log

and found a log entry saying "fixed info leak", which seemed promising. I then did 

> git show 3621de89d8eac9d3b64302bfb2dc67e9a566decd

to see the log entry, and found that the password was in a previous version of the file (4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7)

---

Level 30: 

This level had very much the same setup as the last one, but there was no information in the log. So, I then tried ls -a to see hidden files, but all that I found were a bunch of config files and nothing interesting in there. Then I remembered Github's branch system, which is how multiple people work on code at the same time. I did a bit of googling to find out how to see all branches with git, and came up with the git branch command. To see all branches, I did
> git branch -a

Which showed me another branch called "sploits-dev". Interesting...

> git show /remotes/origin/sploits-dev

but it turned out to have just a bunch of random stuff in it, and nothing that looked like a password. There were still two other branches to check out, though!

> git show /remotes/origin/dev

And, in the dev branch, it showed us a change that contained the password (qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL)

---

Level 31: 

This is another git repo level. There was no sign of anything of anything in the logs and there was also nothing in any other branch, but then I remembered the releases from Github and decided to figure out if this project had any. This immediately led me to tags, another way to see points in the project's history. To check and see if this project had any tags, I did
> git tag

which showed me one tag called "secret". 
Further investigation with 
> git show secret

gave me the password (fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy)

---

Level 32: 

This level wanted me to push a file called key.txt with the contents "May I come in?" to origin. To do this, I first made the key.txt file and then did 
> git commit

but nothing was being committed (even though I just created a file). To figure out why, I did ls -a and found a .gitignore telling git to ignore all txt files. A quick google search told me the way to override this was by doing 
> git add -f key.txt

followed by
> git commit

This pulled up a nano editor asking for a message to go with my commit. The README didn't say anything about a message, so I just wrote "commit" and then sent it. 
After doing this, I pushed my commit to origin/master with
> git push origin master

And, scattered among the terminal messages that git push spat out, there was the next password (3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K)

---

Level 33: 

After I ssh'd into this level, I got a very confusing message "Welcome to the uppercase shell". This was bad, as it meant that every command I knew about (which happened to be written in lowercase letters) didn't work here. So, I googled "What things are uppercase in linux?, and the answer turned out to be only variables. Naturally, it's time to find an environmental variable (special variable containing information about the device / shell) that will activate some kind of normal shell that takes normal commands when run. It turns out the set command lists all the environmental variables, so I logged back into a different banit level and did set to see if there was anything interesting. The first thing that was listed was the path to bash, which was stored in the BASH environmental variable. 

However, none of these seemed to be able to run anything. The most promising things I found were that bash variable from earlier and another environmental variable called $SHELL that contained the name of the shell running. However, some googling found that the $SHELL variable was actually set by the login, which is what put me in this nonsense uppercase shell in the first place. What I needed was to somehow be able to access something underneath the uppercase shell, since I had a feeling it was some kind of strange executable that was running on top of a real shell. 

And then, I found it. The linux environmental variable that contained the process running, $0. It said that if it was invoked inside a script, it would correspond to the script, and if it was executed inside a shell, it would point to the shell. What was different about $0 is that it could actually call that script / executable thing. Time to see what was underneath the uppercase shell!
> $0

And the uppercase shell disappeared and I could do regular command-line stuff again. Turns out, my hunch was correct that there was a real shell underneath the uppercase one. This let me navigate to bandit_pass and grab the final password (tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0)

---

#Final thoughts

Overall, the bandit challenge was super fun. Most of the levels I did not find too difficult, but some (most notably the last one and level 26) were very challenging and required a bit of research into how linux works as an operating system. Overall, I would definitely recommend it, as I feel like the "figure it out" approach is much better when it comes to learning linux.

