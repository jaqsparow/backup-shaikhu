## How to auto change desktop wallpaper every minute using Python

In this fun tutorial we are going to download a random wallpaper from **pexels **and set it to our desktop background every minute using Python. While it sounds like fun project, but we are going to learn some important concepts like how to make http request, how to download a file and save it to your local machine and most importantly how to set the wallpaper using Windows's system function.

Visit my GitHub page for complete codes at  [https://github.com/jaqsparow/change-desktop-wallpaper](https://github.com/jaqsparow/change-desktop-wallpaper) 
> Note: All these code snippets are tested on windows 10, with Python v3.9.0

### What we will build?
We are going to build a CLI app using Python to change desktop wallpaper every given number of minutes. Every wallpaper downloaded from internet will be unique and our app will change the wallpaper based on the time we set.

#### What we will learn in this post?
- How to generate random number?  📗
- How to make http request using Python? 📕
- How to download an image to your local machine?📙
- How to set wallpaper using Python 💡
- How to call windows's C++ system function from Python💡

### Get API key to download wallpaper

First of all, we need an api key to make HTTP request and download image. For this tutorial, I am going to use **pexels**. Follow these steps to get the api key

<a href="https://www.pexels.com">
  <img src="https://images.pexels.com/lib/api/pexels.png" />
</a>

#### Step 1: Register for a free account
Visit this link and then click "Join" link. Provide your name and email details in the text provided and create an account
 [https://www.pexels.com/join-consumer/](https://www.pexels.com/join-consumer/) 

#### Step 2: Get started
Once you registered and confirmed your email visit   [https://www.pexels.com/api/](https://www.pexels.com/api/) and click "Get Started" and it will take you to new page for applications. Click new application and provide your application details. 

After above step you should be provided with an **api key** which we are going to use in the next step. So save this api key somewhere in your machine.

> Note - Do not abuse the *Pexels* api. They are providing 200 requests per hour with a free account. So respect 👏

#### Install requests module
To make an http request, we need a module **requests** . so lets install this module

```
pip install requests
```
Now we have everything setup and lets go to next step to start coding 💻.

### Start coding..
First we need to import all the modules we need to make this app. So lets get started.

```
import argparse
import os
import time
import random
import requests
import ctypes,win32con
```
#### Why we need these modules
- **argparse: ** Since this is a CLI app, we need to pass arguments to our app using this module
- **os: ** We need this module to find the current working directory and full path of our wallpaper
- **time: ** This module will be used to call our function every given minutes and also to use sleep() method
- **random:** To generate random number , so we can make random request url every time
- **requests: ** We are using this module to make http request and to download the wallpaper
- **ctypes: ** This module is to call foreign function in Python. It provides C compatible data types and allows to call DLL and shared libraries. We need this to call **SystemParametersInfoW** to change our wallpaper

#### Generate a random number
Lets create a function named **get_wallpaper()** and generate a random number inside this function, which we will use to make random http request URL.
```
def get_wallpaper():
    #Random number
    num = random.randint(1,99)
```

#### Make http request

Now lets save our api key inside the function as shown below. Replace your api keys in the field *<Your api key goes here>* and also set the *url for pexel request* as shown below

```
    #Set your api key here
    payload = {'Authorization': '<Your api key goes here>'}
    #Search query
    query = 'flower'
    #URL for PEXELS
    url = 'https://api.pexels.com/v1/search?per_page=1&page=' + str(num) + '&query=' + query
```
With above codes, we are creating a random URL with **page** parameter and query =**flower** 🙂. Change the query depending on what type of wallpaper you want to download like nature, mountain, airplane or anything you want. I am downloading wallpaper with query **flower**
So we have the url, now lets make the http request to that url with *get method*

```
    res= requests.get(url, headers=payload)
```
In above code, we are making http request to **Pexels** with api key in the headers. Also note that we have saved the response in **res** variable which we are going to use in our next step.
Now its time to download the image. Lets do that using below code

```
    #Get the url of the image from the response
    if res.status_code == 200:
        img_url = res.json().get('photos')[0]['src']['original']
        #Make request to get the image
        img = requests.get(img_url)
        #Write and save the imgae locally with name temp.jpg
        with open('temp.jpg', 'wb') as f:
            f.write(img.content)
    else:
        print('error in making http request')
```
Lets understand the above code. In the very first line we are checking the status code from our http request in the last step. Please note that for every http request, we will get the response with status_code. If the status code is 200, that means the request was successful and a good response object should be returned. 
We are converting the response content into json object so that we can use it parse the image url. 

In the second line we got the image_url after parsing the JSON response object.
We are making another http request to download the image and finally we are saving the image  in our local system using **write** method. Please note that we are saving the file using **wb** mode so that a new file will be downloaded and replaced the old one. We are saving the wallpaper as "temp.jpg"

Finally we are printing 'error message' if the status code returned is not **200**
#### Set wallpaper using Python

In this section we are going to create a new function **set_wallpaper()** as follow
```
def set_wallpaper():
    get_wallpaper()
    path = os.getcwd()+'\\temp.jpg'
```
Lets take a look what we did above. In the first line inside **set_wallpaper()** we are calling the function *get_wallpaper()* to download the image and save it to *temp.jpg* file. In the next line, we are assigning *path* variable with the complete path of the image that we just downloaded.

#### How to call Windows' system function?
The next line shown below is interesting. With the help of **ctypes** module, we are calling Windows's system C++ function *SystemParametersInfoW()* to change the wallpaper. Lets see different parameters of this function.
```
ctypes.windll.user32.SystemParametersInfoW(20,0,path,0)
```
**Syntax**
```
BOOL SystemParametersInfoW(
  UINT  uiAction,
  UINT  uiParam,
  PVOID pvParam,
  UINT  fWinIni
);
``` 
- **uiAction: ** this is systemwide parameter (such as desktop/icon/menu..)to be set or retrieved.
> We are using SPI_SETDESKWALLPAPER or 0x0014 to set this parameter. Please note 0x0014 is nothing but **20** in decimal which are sending in above code.
- **uiParam:** This is second parameter which depends system parameter of windows. We are sending **0** as for most of the request. as suggested by the usage document.
- **pvParam:** This parameter can be used to send the argument for action set in the first parameter. With this we are sending full path of the image
- **fWinIni:** This parameter can be used to set or update user profile or broadcast the change to all the top level windows. In this case we don't want to broadcast anything , so we are sending **0**

#### Return code
If the above function execution succeeds, the return value will be nonzero else it will be zero

With this we are done with the coding to change and set the wallpaper. Now its time to call this function every minute

#### Call function every minute to change the wallpaper

Here we will be calling from the main function based on the user input. Since this is CLI app, we are going to ask user for what would be the frequency to change the wallpaper? every minute, or every 5 minutes or any other value?

```
if __name__ == "__main__":

    parser = argparse.ArgumentParser()
    parser.add_argument("-t", "--time", help="Enter time in minutes")

    args = parser.parse_args()
    minute = int(args.time)
    while(True):
        time.sleep(minute*60)
        set_wallpaper()
```
With the above code we are setting up the entry point of our app. First two lines are to setup CLI arguments where we are setting up input argument **-t** which will take number minutes we will be calling *set_wallpaper()*. We have used **sleep()** method to delay execution and call **set_wallpaper()** every **n** number of minutes as per user input.

Ok. All done  now lets run the app 🙂

#### How to run the app?
Run in the CMD as shown below.
To run every minute
```
C:\Users\jaqsp>wallpaper.py -t 1
```
Or if you want to change wallpaper every 5 minutes
```
C:\Users\jaqsp>wallpaper.py -t 5
```

### Conclusion
Hope this post will help you understand some important concepts like how we made http requests and downloaded an image to our local system. Also how we used Windows's system function and called it from Python program to change the wallpaper. There are many other parameters available for this system function that we can use to perform some other interesting cases. Visit the link below to know more about this function.

Visit  [my GitHub repository](https://github.com/jaqsparow/change-desktop-wallpaper)  to see complete codes. Thanks for reading:)

#### References

1.  [https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-systemparametersinfow](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-systemparametersinfow)  


2.  [https://requests.readthedocs.io/en/master/](https://requests.readthedocs.io/en/master/) 


#### My other recent posts

- Files, Folders and Python [Files,folders and Python](https://shaikhu.com/files-folders-and-python) 
-  How to keep your computer awake [Keep computer awake](https://shaikhu.com/how-to-keep-your-computer-awake-using-python) 
- How to monitor CPU utilization in Linux  [cpu utilization](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 
- How to schedule jobs using crontab  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 