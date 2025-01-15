
1. Our first task was to figure out the IP address of the machine we were trying to hack into. 
    * To do this, we used the command Nmap to find a list of all the IP addresses currently on the network
    *We started off by asking what our classmates’ computer IP addresses were and found that they all began with 10.0, the third octet was in the range 140-255, and the 4th octet was between 0-255. We only figured this out after trying numerous other nmap switches.
    *Sudo nmap -O 10.0.140-255.0-255
2. Once we had all the IP addresses we began a process of elimination. We started filtering down the IP addresses that we knew. Until we were left with only the 3 machines that were not currently logged in. 
    *From there we began powering down machines and seeing which ones would not ping. We were able to establish that our IP address was 10.0.140.9.
3. In order to log in to the target machine, we tried several methods
    *Attempted to install ssh keys onto the target machine
      *Ssh-keygen
      *ssh-copy-id cseh-6@10.0.140.9
   *When the SSH key attempt proved unusable, we tried using a tool called Hydra to crack the password. The teacher gave us a hint that the password was contained in the file rockyou.txt on his computer. Installing Hydra and getting rockyou.txt proved cumbersome on the machines we had available, but Corey’s machine had both on them. We ssh’d onto his machine and did the following
      *Ssh cseh-0@10.0.140.3
      *Password: security0
      *Find -name rockyou.txt (file turned up in a hidden folder in Trash)
      *Hydra -l cseh-6 -P (path to rockyou) -t 6 ssh://10.0.140.9 (target IP)
      *Hydra did its thing for a couple of minutes and told us that the password to the machine was “qwerty” (very secure). 
4. Opened up the machine and logged in with the password
