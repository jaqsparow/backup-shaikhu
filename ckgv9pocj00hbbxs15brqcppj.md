## How to get email alert when your website is down using shell and Python?

There are many ways to monitor and set alerts for your web server using third party apps but they don't come with free, be it Pingdom or Amazon's CloudWatch. In this post I am going to show  how we can write and setup our own alert tool with just few lines of code.
However before going further into codes let us see what are the commands and utilities available in Linux to monitor web server health. 
> All the below commands are very basic and available on all Linux flavors. I am running these commands on Ubuntu 20.04 LTS for below demos

#### What we will learn in this article?

📗 How to check if a website is up and running or down?</br>
📙 How to write a basic shell script?</br>
📗 How to call Python program from shell script?</br>
💡 How to send an email using Python?</br>

### How to check if web server is up and running?

There are many commands and utilities available to monitor if your website or app is up and running fine. I have listed few of them. Lets checkout

👉  **curl** </br>
👉  **wget**</br>
👉  **ping**</br>

#### Curl 
**curl** is a very powerful utility to send and receive data over many supported protocols such as HTTP,FTP, SMTP...
We are going to use **curl** with **I** options to fetch the headers from a website. Based on the header we can identify if server is down or up. Lets try it

**USAGE: ** curl -I SERVER IP

```
shaikh@ubuntu:~/scripts$ curl -I "https://hashnode.com"
HTTP/2 200 
date: Thu, 29 Oct 2020 17:35:18 GMT
content-type: text/html; charset=utf-8
set-cookie: __cfduid=d89ee33e79708513e09d889a86e8aa34a1603992918; expires=Sat, 28-Nov-20 17:35:18 GMT; path=/; domain=.hashnode.com; HttpOnly; SameSite=Lax; Secure
cache-control: public, s-maxage=1800, max-age=0
etag: W/"4436a-AFOlSSR9igz5hRyOL+SdGTPJfxk"
vary: Accept-Encoding
x-frame-options: Deny
x-powered-by: Next.js
cf-cache-status: HIT
age: 1020
cf-request-id: 061706b33c0000051308a22000000001
``` 
As shown above **curl -I** returns header and the very first line (HTTP/2 200 ) tells us that webserver for **hashnode** is up and running. Note that server is up and running as long as the response code is 200,301,302,308. In above example response code is 200 , so its up.

#### wget

This is another utility in linux used as network down loader. We will use option **-S** to get the headers from a website.
**USAGE:** 
> wget -s SERVER : This will fetch and download the headers and pages of a website

> wget -S --spider SERVER :This will work exact similar way as above except that it will behave as web spider and  wont download the pages.


```
shaikh@ubuntu:~/scripts$ wget -S --spider http://shaikhu.com
URL transformed to HTTPS due to an HSTS policy
Spider mode enabled. Check if remote file exists.
--2020-10-29 10:47:55--  https://shaikhu.com/
Resolving shaikhu.com (shaikhu.com)... 192.241.200.144
Connecting to shaikhu.com (shaikhu.com)|192.241.200.144|:443... connected.
HTTP request sent, awaiting response... 
  HTTP/1.1 302 Found
  Alt-Svc: h3-29=":443"; ma=2592000
  Content-Length: 206
  Content-Type: text/plain; charset=utf-8
  Date: Thu, 29 Oct 2020 17:47:55 GMT
```

As shown above the returned response code is **302** for redirection, so our server is up.

#### Ping
**Ping** is most common utility to check the web server's health. It sends ICMP ECHO_REQUEST to network host to get the up information. We are going to use **-C** count option to send n number of request packets.

**USAGE**
> ping -c COUNT SERVER
> Above command will send COUNT no of request packets to SERVER 


```
shaikh@ubuntu:~/scripts$ ping -c 3 shaikhu.com
PING hashnode.network (192.241.200.144) 56(84) bytes of data.
64 bytes from 192.241.200.144 (192.241.200.144): icmp_seq=1 ttl=55 time=38.2 ms
64 bytes from 192.241.200.144 (192.241.200.144): icmp_seq=2 ttl=55 time=34.0 ms
64 bytes from 192.241.200.144 (192.241.200.144): icmp_seq=3 ttl=55 time=36.0 ms

--- hashnode.network ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 33.954/36.039/38.178/1.724 ms
shaikh@ubuntu:~/scripts$ 
```
As shown above we sent 3 packets and received 3 packets as well, so that means our website is up and running

### How to write a basic shell script?

Now that we know which commands and utility to use to know the status of our website, so lets get started. We are going to issue the command 

#### Step 1 : Create a new file for shell script

Lets create a new file named *alert.sh* and save it to your home folder. We can use any of the above command to know the status of a website. I am going to use **wget**'s web spider option to get the details. However you can use any of them.

Before writing any script, note the exact location of *bash*

```
shaikh@ubuntu:~/scripts$ which bash
/usr/bin/bash 
```
Now lets start writing shell script *alert.sh*

```
#!/usr/bin/bash
MYHOST="https://YOUR WEBSITE ADDRESS"
if wget --spider -S $MYHOST 2>&1 | grep -w "200\|301\|302\|308" > /dev/null
then
echo "server is up"
else
#Server is down"
/usr/bin/python3 send_alert.py
fi
```
This is a very simple script to monitor your web server and send an email notification if its down. Lets understand what we did line by line.

In the very first line we are providing location of *bash* which was found in the previous step above
then we have saved our website address in a variable named MYHOST
From line number 3 we are executing the *wget* command in an *if else* statement. If the return code from wget is other than 200,301,302 and 308 then control will move to else block considering server is down or unreachable.
In this step you can change the command to *ping* or *curl* as you like

In the *else* block we are executing a Python file named *send_alert.py* to send an email notification. You can ignore this step if you are sending email using MAILX of linux package within shell script. However I dont want to install any third party library and want to use existing Python library to send the email.

### How to send an email using Python?
Writing an email using Python is very simple. No third party library is needed to send an email. Here we are using Python's inbuilt library *smtplib* to send an email. 
As shown rest of the code is pretty simple. We have defined **FROM_EMAIL**,**Password**, **TO_EMAIL**,**header** and finally **body**

Lets see what our *send_alert* looks like

```
import smtplib

FROM_EMAIL = 'FROM EMAIL ADDRESS'
PSWD = 'YOUR PASSWORD'

TO_EMAIL = 'TO EMAIL ADDRESS'

#Subject and header
header = 'To:' + TO_EMAIL + '\n' + 'From: ' + 
FROM_EMAIL + '\n' + 'Subject: Alert for shaikhu.com \n'

#Message
body = """
MAY DAY! MAY DAY!! MAY DAY!!!
Your webserver for shaikhu.com is DOWN."""
message = header + body
#Define function to send email
def sendalert(msg):

    smtpObj = smtplib.SMTP('smtp.mail.yahoo.com', 587)
    smtpObj.ehlo()

    smtpObj.starttls()
    smtpObj.login(FROM_EMAIL, PSWD)

    smtpObj.sendmail(FROM_EMAIL,TO_EMAIL,msg)
    smtpObj.quit()
if __name__ == '__main__':
    sendalert(message)

```

Congratulations!!🥳. We have just wrote our own script to monitor our server and send us an email notification if it is down. Now all we have to do is put this script on a scheduler or cron table so that the script will run every **n** no of minutes and keep monitoring our website.

#### Add the job to scheduler or crontab

If you want to know how to setup job scheduler using crontab then please do check my article  [https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 

```
*/5  *  *  *  *  sh alert.sh
```
As per above cron table, our alert.sh will be executed every 5 minutes. That means alert.sh will check our website health every 5 minutes and will send me an email if the website is down. 

This is how the email from my script looks like.


![email.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603999870746/vbeSw5dUa.png)

### Conclusion

Though there are many websites are providing website alert service but not all of them are free. As seen in this article with just few lines of code, you can build your own alert app that will keep monitoring your website every moment and will send an email notifications when its down.

We have learned some important commands and codes such as how to check website is down or up and how to send email using Python and also how to write a basic shell script.

Hope this tutorial helped you learn something new today. Let me know your thoughts in the comment section 😃.

#### References

1.  [https://docs.python.org/3/library/smtplib.html#smtplib.SMTP.sendmail](https://docs.python.org/3/library/smtplib.html#smtplib.SMTP.sendmail) 


#### My other recent posts

- How to change desktop wallpaper every minute using Python  [Change desktop wallpaper](https://shaikhu.com/how-to-auto-change-desktop-wallpaper-every-minute-using-python) 
- Files, Folders and Python [Files,folders and Python](https://shaikhu.com/files-folders-and-python) 
-  How to keep your computer awake [Keep computer awake](https://shaikhu.com/how-to-keep-your-computer-awake-using-python) 
- How to monitor CPU utilization in Linux  [cpu utilization](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 
- How to schedule jobs using crontab  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab)  










