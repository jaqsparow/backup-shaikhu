## How to trigger a job or program from anywhere with a SMS

Its holiday season every where and we don't want to open laptop and system all the time when travelling and enjoying our personal time. In this post, I am going to show how we can run some important jobs or programs with just one SMS when we are on the go.

### Introduction
In this tutorial we are going to learn how we can trigger a job or program with just one SMS. For this tutorial we are going to use **Twilio** API for sending and receiving SMS. With just few lines of codes, we will implement a very basic app to receive sms and trigger jobs based on the sms. So lets check out what we are going to learn in this tutorial.

#### What we will learn in this article?
📗 How to setup programmable SMS api on Twilio? </br>
📙 How to write  a basic Flask app? </br>
📗 How to setup **ngrok** to access your app outside localhost?</br>
📙 How to run a shell command from Python?</br>


 ### How to setup programmable SMS api on Twilio? 

In the very first step, we are going to need a mobile number and API where we can send a SMS and run a script. For this purpose, we are going to use **Twilio**'s beautiful and easy to use API to setup our SMS app. 

#### Get your free $10 credit to use Twilio SMS api

Twilio is providing **$10 credit** for new user signup and interesting thing about **Twilio trial account** is that you don't need to have a credit card to try its free service. Please  [click here and visit Twilio](www.twilio.com/referral/ZZMzrs)  to create a free account and get $10 credit to use its SMS api during trial period.

- Once your free account is created, login to your account and create a project from top right corner of your dashboard.
- Then go to **All products & services**  from the left tab and click ** # Phone numbers**. You should see screen similar to below. Click **Buy a Number** and get a new number where we will send a SMS.
![twilio.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604168967939/H-mXW1yDF.png)

Now that we have a number registered, lets go to next step and setup a Flask app.

### How to write  a basic Flask app? 
As I mentioned we are going to use a very basic Flask app. Before writing our Flask app, lets install virtual environment so that our app will be running independently with the packages installed within the environment.

#### Step 1: Create virtual environment
- Lets install virtual environment.
```
pip3 install virtualenv
```
- Now create a virtual environment within a new directory smsapp
```
shaikh@ubuntu:~/scripts/smsapp$ virtualenv venv
```
- Activate the virtual environment
```
shaikh@ubuntu:~/scripts/smsapp$ source venv/bin/activate
(venv) shaikh@ubuntu:~/scripts/smsapp$ 
```
#### Step 2: Install Flask
- Install Flask app using below entry
```
pip3 install flask
```
- Lets write very basic flask app with below lines of code.

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == "__main__":
  app.run()
``` 
- Run this app with *python3 app.py* as shown below and check the result at *http://localhost:5000*

![flask1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604172015050/2oehMXhN7.png)

You should see the output "Hello World!" in your localhost at port 5000. As shown above you can check the output at *http://localhost:5000*

#### Step 3: How to install and access app using ngrok
Now that we have installed our flask app and able to run it in our localhost but we cannot access our app outside our local network. To access this app over internet lets install **ngrok** which will allow us to expose our app over the internet.

- Install this with below command (for Ubuntu)
```
snap install ngrok
```
- Activate ngrok to expose our app to the internet.
```
python3 app.py &
ngrok http 5000
```
This is how it will look once we run above commands


![ngrok1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604172783909/_xoC7Pzak.png)

- We need to use the ngrok http URL from above output. Lets checkout the app at this url on any device over the internet.

![ngrok2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604172852487/x04qK7TFX.png)

### How to run a shell command from Python?
To run a shell command from Python, we will use **OS** module's special function **popen()**.
```
os.popen(command)
```
So this is how our Flask app looks like if we want to run a command using **popen()**

```python
from flask import Flask
import os
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
@app.route('/command',methods=['POST'])
def command():
    command = "/usr/bin/python3 ~/scripts/send_alert.py"
    os.popen(command)
    return 'Command executed'

if __name__ == "__main__":
  app.run()
```
Please note that we have created a new route **/command** to execute the command to run another python script **send_alert.py**. You can execute any command you want. For this example I am running a script to send email alert.

So this is how our Flask app will trigger a new process and execute the command. Now we are going to configure **Twilio** so that when we send sms to its number, Twilio should trigger a HTTP request to our **/Command** route.

Lets head over to **Twilio** again and click the mobile number you purchased in previous steps and then update the webhook  for "A message comes in" as shown below


![twiliosms.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1604249417727/h7eaz9GqH.png)

Now when we send a sms to Twilio number, It will make HTTP Post request to above link and when it does, our *command()* function is going to be executed 😃

Now if you want to run multiple jobs based on SMS body (like JOB1,JOB2) we need to install Twilio library so that we can parse SMS and run the jobs accordingly.
Here is the app.py looks like with that feature.

```python
from flask import Flask, request
from twilio.twiml.messaging_response import MessagingResponse

app = Flask(__name__)

@app.route('/command', methods=['POST'])
def command():
    number = request.form['From']
    message_body = request.form['Body']
    action = message_body.split()[0]

    response = 'command ' + action + ' executed'

   if action.lower()== 'job1':
        command = "/usr/bin/python3 ~/scripts/job1.py"
        os.popen(command)
        
   elif action.lower()== 'job2':
        command = "/usr/bin/python3 ~/scripts/job2.py"
        os.popen(command)
   else:
        response = "Invalid request

    resp = MessagingResponse()
    resp.message(response)
    return str(resp)

if __name__ == '__main__':
    app.run()

```

As shown above, with the help of **Twilio** library we are able to parse text message and run jobs based on incoming message content. Now if we send a text 'JOB1' to the **Twilio** mobile number, Twilio will make a HTTP POST request to our flask app and execute our job1 script. Once job1 script is executed a return sms will be received on your mobile number that 'command executed' else you will receive 'Invalid request' in case you send some invalid job requests:)
In above Flask app, you can replace the jobs or program names and add any number of programs or commands or even shell scripts if you want to execute them.

#### Output:

Here is the output of above script and this is how our app should work.


![1IMG_9085.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1604266100085/FuxnbdSlP.jpeg)


### Conclusion
Congratulations on building your SMS app to run a job with just a SMS🥳.. In this article we learned some interesting concepts like how to run a shell command from Python script and how to integrate third party sms api to our FLASK app. 

Hope this will help you build some amazing sms apps. Let me know your thoughts in the comment section and dont forget to read my other interesting posts in below links.

Thanks for reading this article. Hit like if you want more such posts 😊

Visit my GitHub page for complete code at  [GitHub](https://github.com/jaqsparow/run-a-job-by-sms) 

#### References

1.  [https://docs.python.org/3/library/os.html#os.popen](https://docs.python.org/3/library/os.html#os.popen) 
2.  [Twilio sms api](https://www.twilio.com/docs/sms) 
3.  [Flask documentaion](https://flask.palletsprojects.com/en/1.1.x/) 
4.   [ngrok](https://ngrok.com/docs) 


#### My other recent posts

- How to get email alert when your website is down using shell and Python?  [https://shaikhu.com/how-to-get-email-alert-when-your-website-is-down-using-shell-and-python](https://shaikhu.com/how-to-get-email-alert-when-your-website-is-down-using-shell-and-python) 
- How to change desktop wallpaper every minute using Python  [Change desktop wallpaper](https://shaikhu.com/how-to-auto-change-desktop-wallpaper-every-minute-using-python) 
- Files, Folders and Python [Files,folders and Python](https://shaikhu.com/files-folders-and-python) 
-  How to keep your computer awake [Keep computer awake](https://shaikhu.com/how-to-keep-your-computer-awake-using-python) 
- How to monitor CPU utilization in Linux  [cpu utilization](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 
- How to schedule jobs using crontab  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 



