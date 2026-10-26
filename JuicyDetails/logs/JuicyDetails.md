Falling asleep in a train so ill keep it short 

**Given**
We received 3 files from the ctf:
access.log, auth.log vsftpd.log

access.log - explains how the credentials were stolen (tools, time, protocols)
auth.log - tells inforamtion about ssh connections
vsftpd - tells inforamtion about ftp connections

**Reconnaissance:**

***Question 1***
*What tools did the attacker use? (Order by the occurrence in the log)*
Answer: nmap, hydra, sqlmap, curl, feroxbuster
Why: When going through access.log file, the column with fingerprints displays known tools based on their fingerprints,
so after scrolling for a bit we can easily spot the tools that were used 

***Question 2***
*What endpoint was vulnerable to a brute-force attack?*
Answer: /rest/user/login
Why: In the column that describes the requests this endpoint repeats itself while hydra was in use

***Question 3***
*What endpoint was vulnerable to SQL injection?/What parameter was used for the SQL injection?*
Answers: rest/products/search and 'q' as a parameter for the injection
Why: Refering to our logs where we saw evidence of the attacker using SQLmap, we can see that they used the following directory and parameter
rest/products/search?q... 

***Question 4***
*What endpoint did the attacker try to use to retrieve files? (Include the /)*
Answer: /ftp
Why: found a line: GET /ftp HTTP/1.1 200. 200 is a successful http request so yes...

**Stolen Data**
***Question 1***
What section of the website did the attacker use to scrape user email addresses?
Answer: product reviews
Why: Following the initial nmap scan, we can see plenty of traffic coming through as the attacker crawls through individual items in a particular section of the website and sends whoami requests to discover changes in the user environment.

***Question 2***
Was their brute-force attack successful? If so, what is the timestamp of the successful login? (Yay/Nay, 11/Apr/2021:09:xx:xx +0000)
Answer: Yay, 11/Apr/2021:09:16:31 +0000
Why: Remembering that the response code for a succesful http request is 200, we can go back through the logs displaying the attacker's use of Hydra and discover a succesful login request and timestamp:

***Question 3***
What user information was the attacker able to retrieve from the endpoint vulnerable to SQL injection?
Answer: email, password
Why: Refering to the section of the logs where we uncovered SQL injection attempts, we can identify a particular set of data that was succesfully retrieved by the attacker:

***Question 4***
What files did they try to download from the vulnerable endpoint? (endpoint from the previous task, question #5)
Answer: coupons_2013.md.bak, www-data.bak
Why: There are a couple giveaways for this: at the very end of the "access" file, and in the "vsftpd" file. The latter shows us that the attacker was succesful in downloading the files over FTP.

***Question 5***
What service and account name were used to retrieve files from the previous question? (service, username)
Answer: ftp, anonymous
Why: Referring once more to the "vsftpd" file, we can see a few unsuccesful attempts to log in under "anonymous", followed by a succesful attempt. This tells us which username the attacker was able to utilize to retrieve the files from the previous question.

***Question 6***
What service and username were used to gain shell access to the server? (service, username)
Answer: ssh, www-data
Why: Finally, the logs in our "auth" file help us answer this question. We can see that after many failed attempts, the attacker is able to log in and create an SSH session.

