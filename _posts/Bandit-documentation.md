Hello! In this post, I will document my solutions to the levels of (Overthewire's bandit)[https://overthewire.org/wargames/bandit/].
---
Level 0: goal is to log in to bandit's server using ssh using port 2220. The password was provided, so all I had to do was write the command.
> ssh bandit0@bandit.labs.overthewire.org -p 2220
> password: bandit0
---
Level 1: The password was stored in a file called "readme" in the home directory, so I did the following
> cat readme
and got the password (ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If])
