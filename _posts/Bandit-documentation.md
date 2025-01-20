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
