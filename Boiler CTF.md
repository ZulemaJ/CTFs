
![boilerCTF](https://github.com/user-attachments/assets/3c9e20a1-1d83-472d-a64e-83935ede5c90)

# Boiler CTF - Intermediate level CTF 


## Task 1: 

- First of all, we run an nmap scan to discover all the open ports on the target: 


<img width="533" height="230" alt="Nmap-scan" src="https://github.com/user-attachments/assets/a5ac8676-af80-473c-b3d5-faa689f48263" />


We can see ports 21, 80, 10000, 55007 are open. 


- We try then anonymous login in FTP: 

<img width="252" height="126" alt="Screenshot 2026-01-28 alle 09 45 52" src="https://github.com/user-attachments/assets/6da9b897-f1dd-43dd-8ba6-e68618c28eb1" />

Anonymous login allowed. 

- The file extension in the current directory will be the answer of question 1.


<img width="429" height="183" alt="Screenshot 2026-01-28 alle 09 48 21" src="https://github.com/user-attachments/assets/7c1a261c-613a-456a-8c33-f8bc61147e24" />



- Let's now run a Version and Script Scan ( -sV -sC ) in order to understand which services are running on open ports:


<img width="581" height="468" alt="Screenshot 2026-01-28 alle 09 57 47" src="https://github.com/user-attachments/assets/85b2e651-b7bb-4011-8f0c-fee3f6b65819" />

Answers to questions 2 and 3 can be easily found from the scan result. 


- Question 5 can be answered Googling the version of the Service running on port 10000, trying to find existing CVEs (if any).


- To answer to question 6, it is enough to run a dirsearch in order to discover hidden directories. 
(You can use whatever tool of your choice)


<img width="631" height="197" alt="Screenshot 2026-01-28 alle 10 12 15" src="https://github.com/user-attachments/assets/d996650a-8b85-44cd-8022-6ee22529233c" />


- Just continue to enumeate directories recursively, until you find something juicy.

  After having discovered several directories under /joomla, and had a look into each of them, I noticed that the /_test/ directory had something unusual: 
  **sar2html**

  
<img width="804" height="431" alt="Screenshot 2026-01-28 alle 10 35 12" src="https://github.com/user-attachments/assets/297f9f03-82d6-4a3b-807d-0bbbe1342947" />


I actually don't know that much about it. 
After Googling it, i popped out that it is a script (usually Perl or Python) that converts system performance data collected by sar into a readable HTML report. 
It can be dangerous if exposed via a web server or running un outdated version. 
Let's search for some exploit if any. 


AND VOILA' : Let's try this Remote Command Execution found on DB-Exploit out.


<img width="954" height="346" alt="Screenshot 2026-01-28 alle 10 41 16" src="https://github.com/user-attachments/assets/5386b437-1fe8-4800-80cb-c2ffeeec6e8c" />


Let's just follow the explot and modify the following URL : http://IPADDR/joomla/_test/index.php?plot=NEW (after having clicked on NEW in /_test) , replacing "NEW" with ";<COMMAND>": 

Let's say: http://IPADDR/joomla/_test/index.php?plot=;ls 

We just found the answer to question 7. 


## TASK 2:

- Once I found the juicy file with the "ls" command, let's "cat" it out:

<img width="765" height="347" alt="Screenshot 2026-01-28 alle 10 47 34" src="https://github.com/user-attachments/assets/cb0a39ea-de3a-46dd-b998-3fb9eca2c6bb" />


- We just jound credentias, to connect with to ssh -p 55007 :

<img width="252" height="15" alt="Screenshot 2026-01-28 alle 10 57 53" src="https://github.com/user-attachments/assets/7434d72c-8ac0-44f9-883a-f181e241d9cc" />


- After connecting, in the homepage we will find the file with the other user's password inside (question 8).


  " su stoner " , we insert the password found in the file and there we go.

  The user.txt is located in the home directory (question 9).


### Time for Privilege Escalation: 

If i look for "sudo -l" i'm gonna have just misleading information. 

I'm gonna look for some SUID files that i can execute with sudo privileges: 

find / -perm -u=s 2>/dev/null 

<img width="344" height="289" alt="Screenshot 2026-01-28 alle 11 23 48" src="https://github.com/user-attachments/assets/2dfe9080-3897-4583-bb19-e01f2f695bd8" />

/find is definetly the executable that i'm gonna use to elevate privs. (Question 10). 


Let's run the code to spawn a root bash and cat the last flag: 


<img width="349" height="86" alt="Screenshot 2026-01-28 alle 11 25 12" src="https://github.com/user-attachments/assets/cbbabef4-1e2f-4d96-8565-bb8120e09333" />



