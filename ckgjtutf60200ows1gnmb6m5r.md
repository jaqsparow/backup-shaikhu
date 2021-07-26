## How to keep your computer awake using Python

This sounds very funny but how can you let your team know that you are always online during work from home and doing your important stuffs 🙂. Here I will show how we can write a Python script with just few lines of code to keep our computer awake all the time. 
In this post, we are going to write a CLI script to keep our computer awake.

### What this script will do?

This script will take number of minutes as input and will keep right clicking every minute until it reaches the input time. We can add additional code to shutdown or restart the system once it reaches the countdown. It will also log the current system time in a text file and takes screenshot of the system before shutting down 🙂. 

**So what this CLI script will do?
**
1. Takes number of minutes (stop_time) as input (No. of minutes computer will awake)
In a loop perform these after initiating a counter to 0 
 - Sleep for 60 seconds
 - Perform mouse click to awake computer
 - Increase a counter every minute from 0 to  stop_time
2. Save the logs of system 
3. Take screenshot
4. Shutdown, Restart or do nothing if no action is provided by user

### What we are going to learn with this script
Well with this script you can pretend to be working and will always be online 😁 but in real you are going to learn few interesting things like
1. How to do mouse operation such as right click  using Python 📙
2. How to write a file in Python 📗
3. How to check current system time 
4. How to take screenshot 📙
5. How to shutdown or restart computer using Python 📕
6. How to write CLI script in Python

### Start coding

First of all lets import few libraries that we are going to use.

```
import argparse
import pyautogui
import time
import os
``` 
**Why we are using these libraries?**
- **argparse** : Since we are writing CLI script, we need to pass arguments using this library
- **pyautogui** : For mouse operation and screenshot of the system
- **time** : to capture current system time
- **os** : to shutdown and restart the system

Now lets write a new function named timer which will take two inputs i.e. input time or stop time and action to be performed. Please note that the input time is a integer variable which is number of minutes to keep our computer active

```
def timer(stop_time, action):
    counter = 0
``` 
Above we have defined a new function timer and initialized a counter to 0
Next we are going to start a loop until counter reaches the stop_time.


```
    while stop_time > counter:
        counter = counter + 1
``` 
Above code will execute and end within a moment but we want to keep system active for stop_time. To achieve that we are going to sleep for a minute and then do right click for each iteration


```
        #Lets sleep for 60 seconds only
        time.sleep(60)
        pyautogui.click(button='right')
``` 
Lets understand what we did above.. 
**sleep(n) **is a special python function to delay or pause further execution of codes for **n** no of seconds. Here we are delaying execution for 60 seconds in each iteration.
 After 60 seconds we are doing mouse operation ('Right click') using Python library **pyautogui**

Next we are going to capture current system time using below instruction

```
    #Save current date and time
    timestamp = time.strftime('%d-%b-%Y_%H-%M-%S', time.localtime())
``` 
We used **strftime** function which convert local time to any readable format you want.  [Visit this link for more details on the format](https://strftime.org/) 

Now lets write the log in a text file named "shutdown_logs.txt". Please note we are using mode **a+** so that we can append the log in the same file for each script run. You need to update the location of the text file accordingly

```
#Create a text log when system shuts down
f = open('C:\\Users\\jaq\\Snapshot\\shutdown_logs.txt','a+')
``` 
Next we are going to write the log in the text file and close the file

```
   f.write('Shutting down at {}'.format(timestamp))
   f.close()
``` 
**Take screenshot** before shutting down


```
#Take screenshot
image_location = 'C:\\Users\\jaq\\Snapshot\\image_' + timestamp + '.jpeg'
pyautogui.screenshot(image_location)
``` 
In the last part we are going to issue shutdown or restart command based on user inputs


```
#Shutdown system or Restart
if action in ['s', 'S']:
    os.system("shutdown -s -f -t 0")
if action in ['r', 'R']:
    os.system("Shutdown -r -f -t 0")
``` 
 If you have noticed 'action' is nothing but the function parameter in the very first line of code. Depending on the action parameter, we are issuing command to shutdown or restart using **system()** function of **OS** module.

With this , we are done with timer() function. Now we need to call it from our main function. So lets defined our function as below.


```
if __name__ == "__main__":

    parser = argparse.ArgumentParser()
    parser.add_argument("-t", "--time", help="Enter time in minutes")
    parser.add_argument("-a", "--action", help="want to shutdown?")
``` 
Lets understand what we did above. We are using **argparse** module to create our user friendly command line interface or CLI. In the first line we are creating **ArgumentParser **object and then in the next two lines we are adding two arguments for time and action input.

Next we will be calling timer function which we defined earlier. We will call the timer function only if we have the input time argument.


```
    args = parser.parse_args()
    if args.time:
        timer(int(args.time), args.action)
``` 

That's it, we completed writing a CLI script in Python to keep our computer awake.

### Conclusion
If you want to take a look at complete code, its available in my Github repository here  [https://github.com/jaqsparow/keep-system-active](https://github.com/jaqsparow/keep-system-active) 
Though it sounds funny that we wrote a script to keep our system active or pretend that we are always online :), however in this post, we learned some important Python modules to do mouse operation, to write a file, to take screenshot and to shutdown or restart computer all with just few lines of code. We have also covered beautiful python CLI module **argparse** which is easy and efficient way of writing command line interface scripts.
 
 
### complete code in Github
Visit this Github page for the complete script  [click here](https://github.com/jaqsparow/keep-system-active) 









