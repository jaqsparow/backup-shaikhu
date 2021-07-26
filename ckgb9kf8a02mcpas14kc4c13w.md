## How to schedule and manage tasks using crontab

Before going into details about crontab, lets suppose you are running an online store and you want to send an email about latest deals on every Friday at 9AM MST. So how you can achieve this. Of course you can write your own job scheduler of n number of lines of codes if you have that much of time or you can simply use crontab available in all Unix and Linux operating system to schedule a task.

### What is Crontab

Crontab is short form of cron table. The cron is a utility available on all Linux and Unix operating system that runs a task or process at a given date and time. So  crontab is actually a table that contains script or commands along with date and time to be run at.

### How to view the crontab or cron table
On Ubuntu you can use crontab -l to view the current table

```
shaikh@shaikhu-com:~$ crontab -l
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

``` 
Use crontab -e to edit and manage cron table

### How to set date and time of job run 
Below is the format of crontab that is supported by all Linux systems.

```
# * * * * * command to be executed
# | | | | |
# | | | | |
# | | | | |
# | | | | |_______________Day of the week (0 - 6)(Sunday to Saturday)
# | | | |_______________Month (1 - 12)
# | | |_______________Day of the Month(1 - 31)
# | |_______________Hour(0 - 23)
# |_______________Minute(0 - 59)
``` 

Lets get into some examples for better understanding

**Example 1:** Lets get into the first example we discussed at the beginning i.e.
Run a job to send an email to all subscriber at 9AM every Friday


```
0  9  *  *  5  /usr/bin/python3 sendEmail.py
``` 

**Example 2:** In below example, we are running a job every 15 minutes( Note / operator)


```
*/15  *  *  *  *  doSomething.sh
``` 
**Example 3:** Here we are running a job every 1st and 5th hour ( Note the comma)


```
*  1,5  *  *  *  doSomething.sh
``` 

**Example 4:**  Below job runs on 1st January every year at 7AM 


```
0  7  *  1  *  happyNewYear.sh
``` 

### What if you want to run a job at random minutes every hour


As of now we have seen how crontab can be used to schedule a job at a specific date and time. But what if we want to run a job at a random time or say random minute every hour. Well we can achieve this as well by writing a **sleep** command as shown below.

```
0  *  *  *  *  sleep $(($RANDOM%60))m;sh test.sh
``` 
Lets understand what we did above. As per crontab rules above command set will be run at every hour. At the start of every hour, the cron will encounter the two commands and the first command is to sleep for random minutes. So this cron will delay for this random minutes and then it will execute the second command which is our job once it wakes up from sleep 😃

If you want to do some more stuff than just sleep, we can write a shell script for the same instead of sleep.

As shown below you can run a python script from shell script after delaying n no of minutes.

**Crontab:**
```
0  *  *  *  *  sh test.sh
``` 
**test.sh**
```
#!/usr/bin/sh
#test.sh

TIME=$((RANDOM%60))
sleep "${TIME}m"
#Do some stuff
#Do some more stuff
/usr/bin/python3 /mybots/newsbot.py
``` 
So basically you are running a python code from a shell script once after delaying RANDOM minutes and after doing some more code stuffs:)

### Conclusion

Cronjob is a very helpful utility table that is being used to schedule a task. Rules are very simple and easy to remember. You just have to follow the correct format to run a task or script. Hope this help you schedule your script/jobs :). 

































