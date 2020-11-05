## How to setup alarm for CPU usage using IFTTT ?

Monitoring CPU and memory usage are one of the top todo checklist for a backend engineer. Sometimes you wont even notice when your server is down due to high CPU usage unless you login and manually check the system. In this tutorial, we are going to setup alarm for CPU Usage by most easy way possible(at least I think so) using a simple shell script and IFTTT web hooks. 

#### What we are going to learn?

📗 What is IFTTT and how to create an applet?</br>
📙 How to check CPU usage of a system?</br>
📗 How to make web request to IFTTT from your server?</br>
📙 How to write a script to check CPU usage and schedule it on crontab?</br>

### What is IFTTT?
IFTTT stands for If This, Then That. It is simple web based service that lets you connect your device, server or apps. It is used to create conditional statements or actions that triggered by the changes in your apps, devices or servers. So it helps automate your tasks for your web apps and devices. Lets get started.

First we need to create a free account and login to  [IFTTT](https://ifttt.com/)

To use IFTTT, we need to create an applet (Free account can create up to 3 applets)

#### How to create applet to set alarm?

Go to the top right and click **Create** link. You should see similar to below page. Now click Add button on "If this add" link

![iftt1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604502596167/cOG-7hpQR.png)

On next page, You need to select a service. In this case we are going to use IFTTT web hook. So search for web hook and select it to set it up. Click "Receive a web request" and set the event name.

![iftt2.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1604503404609/CScCSZIoE.png)

As shown below we are setting the event name as "high_cpu_usage" and then we will click "Create Trigger" to create the trigger.


![iftt3.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1604503636298/guAT2DByX.png)

So far we have created the trigger event, next we are going to create action service like what action needs to be taken if the above event is triggered. I am going to set EMAIL service so that an email will be generated when CPU usage will be high. 

So click "Then That Add" and choose EMAIL service and set the subject and body of the email that will be sent to your account. Then Review and finish the setup.


![iftt4.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1604504102228/Rfsq-nyWh.png)

#### How to make web request to IFTTT?

At this moment we are done with setting up our IFTTT applet. Now we need to know the complete web URL of our applet so that we will make web request to notify cpu usage is high. 
To do that go to your account and lick my service, then webhooks and then 'documentation' page on the top right corner. You should see something like below.


![iftt5.PNG](https://cdn.hashnode.com/res/hashnode/image/upload/v1604504527269/y0E8O4G4y.png)

We need to make a small change to add event name to the above URL.
So our correct URL to make web request should look like this and this is how are going to make a web request to IFTTT.

```
curl -X POST https://maker.ifttt.com/trigger/high_cpu_usage/with/key/boJOWPwAx6x2qjAWr3CUym
```
> I have deleted **webhook** created for this tutorial. So please don't use the above key, Its not going to work 😃.

### How to check CPU Usage?

There are many ways to check CPU usage of a server. I have already posted a complete article for CPU Utilization and we are not going to cover in detail here. Please go and check my article if you are interested  [https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 

For this tutorial, we are going to use VMSTAT command. This command will tell current CPU and memory usage of the system. 
Below command will tell CPU usage every second for 5 times. So with this command we can check CPU usage for last 5 seconds. We are going to use this entry in our next shell script to check the cpu usage.

```
VMSTAT 1 5
```
#### Shell Script to monitor CPU Usage

Since we got the idea how to check CPU Usage lets start writing a simple script to monitor CPU usage.
Here is how our script looks like. 

```
#!/usr/bin/bash
#Set Threshold for CPU Usage
THRESHOLD=90.0
#CPU usage for last 5 seconds
cpu_usage=$(vmstat 1 5 | awk '{if(NR > 2) {sum+=$15}} END {print 100-sum/5}')
if [ $(echo "$cpu_usage > $THRESHOLD" | bc) -eq 1 ]
then
#SET ALARM using IFTTT webhook
curl -X POST https://maker.ifttt.com/trigger/high_cpu_usage/with/key/dhFYYNRL4to_1k5O1NOUd3
fi
```
Lets understand the above script.
- First line is the bash interpreter location.
- In the second line we are defining a constant for CPU threshold value.
- In the next line we are executing VMSTAT command and saving CPU usage in a variable named cpu_usage
- In the next line no 4, we are comparing CPU usage value with the threshold value set in the second line. 
- If the CPU usage is higher than the threshold, we are making a web request to IFTTT with the use of **CURL** command, That's it! Lets save this script as *cpu_alert.sh* in your system.

#### Run this CPU usage script every 5 minutes.

With the help of crontab we are going to schedule this shell script ,so that script will be running every 5 minutes to monitor our CPU health.

```
*/5  *  *  *  *  sh cpu_alert.sh
```
With this setting in crontab, our script will run every 5 minutes and check CPU usage. If you want to know more about how to set crontab and schedule jobs, then check my post  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) .

If the cpu usage is higher than Threshold, it will make a web request to IFTTT and IFTTT will send us an email alert  😃. Now how easy that was!!!

#### Output of above work

This is how IFTTT email looks like when CPU usage reaches the threshold limit.


![gifgit.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604510681337/Tyw6oxi6K.png)

### Conclusion
IFTTT is a very easy and also powerful web based service that can be used to connect your apps and devices. As shown in this tutorial, with just few lines of script, we have build a powerful tool to monitor our system's CPU usage. Same logic can be used and extended for memory usage as well.
The amazing thing is that we are not writing codes to send an email. We are just making a web request conditionally to IFTTT and IFTTT is sending us the email alert.

Hope this tutorial will help you build some amazing apps using IFTTT. Let me know your thoughts in the comment section and dont forget to check my other **How to** tutorials listed below. Thanks!

#### My other recent posts

- How to trigger a program by sending an SMS [https://shaikhu.com/how-to-trigger-a-job-or-program-from-anywhere-with-a-sms](https://shaikhu.com/how-to-trigger-a-job-or-program-from-anywhere-with-a-sms)
- How to get email alert when your website is down using shell and Python?  [https://shaikhu.com/how-to-get-email-alert-when-your-website-is-down-using-shell-and-python](https://shaikhu.com/how-to-get-email-alert-when-your-website-is-down-using-shell-and-python) 
- How to change desktop wallpaper every minute using Python  [Change desktop wallpaper](https://shaikhu.com/how-to-auto-change-desktop-wallpaper-every-minute-using-python) 
-  How to keep your computer awake [Keep computer awake](https://shaikhu.com/how-to-keep-your-computer-awake-using-python) 
- How to monitor CPU utilization in Linux  [cpu utilization](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 
- How to schedule jobs using crontab  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 







