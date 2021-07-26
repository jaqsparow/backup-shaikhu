## How to develop a Twitter bot using python and Nasa api

Hello everyone!

This is my first ever blog post and here I am going to share about a twitter bot that I developed in early 2018. The bot we are talking about is  [@Nasarobot](https://twitter.com/nasarobot)  . This bot tweets one beautiful astronomy image everyday using Nasa api with just few lines of python code. So lets get started..

### Step 1: Signup for twitter developer account

To create a twitter bot, first thing we need to do is signup for a developer account at  [https://developer.twitter.com/en](https://developer.twitter.com/en) 
Once signup is done, lets go to the 'Apps' menu and create a new app. Do submit details as asked like the name of the app, its description etc. After creating an app you will be provided API Key, API Secret Key, Access Token and Access Token Secret as shown below.


![twitter_keys.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1601176618473/kY8G60JoW.jpeg)

### Step 2: Signup for Nasa API Key

Since this bot tweets astronomy picture provided by Nasa open API, we also need to signup for a free key at  [https://api.nasa.gov/ ](https://api.nasa.gov/) 
Once you signed up at above link, you will be provided an API Key that we need to fetch an image from Nasa APOD (Astronomy Picture of the Day).

### Step 3: Lets start coding

At this point we have all the keys and secrets we need to  make an HTTP request to twitter and Nasa api. Lets create a new folder and a new file named 'keys.py' inside it and copy all the keys and secret from above links into this file.

So here is how keys.py file should look like


```
#Twitter keys and secrets
API_KEY= 'Agh73979Ukjgkjhkyrowrnnva'
API_SECRET= 'Ajkhskjafhljkfhiepriue9r739bhh39r8y3983jkjdlfkjgju'
ACCESS_TOKEN= '988888667627524654-ZdfdljfuriueriowwopwbGFUgiHKuou'
ACCESS_SECRET= 'Ghiosfuaiouiurieytityoeityoein798nBJK989HKkl7'

#Nasa API Key
NASA_API_KEY = 'VuiwryiThjgj9789HKkH0808jhjgkjglh97986gd'
``` 
(Please don't copy these keys, these are dummy keys and wont work 😃

To tweet programmatically using python we need install a third party library called tweepy using **pip install tweepy** and also we need request module to make HTTP request using python. So lets install it using **pip install requests**

Now lets create a new file called nasabot.py and put below lines

```
import tweepy, requests, os
from keys import *

``` 
At this point we have imported all the modules required, lets create our entry point i.e. the main method


```
if __name__ == "__main__":

    # NASA Image API access
    HOST = 'https://api.nasa.gov/planetary/apod?api_key='+ NASA_API_KEY
    nasa_obj = requests.get(HOST)
``` 
With above code we made a HTTP request to Nasa rest api and we saved json object as nasa_obj. If you want to check how json response looks like , go to this link with DEMO key  [https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY](https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY) 
From this response object we are going to need 'copyright', 'title' and 'url' of image. So lets get those and build a message that we will tweet with below codes.


```
    if 'copyright' in nasa_obj.json():
        message = 'Astronomy Picture of the Day: ' + nasa_obj.json()['title'] + ' copyright : '+nasa_obj.json()['copyright'] + ' #APOD'
    else:
        message = 'Astronomy Picture of the Day: ' + nasa_obj.json()['title'] + ' #APOD'
		
    if 'url' in nasa_obj.json():
        url = nasa_obj.json()['url']
    else:
      url = nasa_obj.json()['hdurl']
   if nasa_obj.json()['media_type'] == 'image':
      tweet_image(url,message)

``` 
At this point we have message to be posted on Twitter and image url from which we will download the image from Nasa. The last 'if' statement checks the media type from json response and calls tweet_image() if media type is 'image.
Since Twitter requires every request to its api to use OAuth for authentication, this is why we are using Tweepy module. Lets put these below codes in a new function in nasabot.py


```
def twitter_api():
    #Lets authenticate our app with OAuth
    auth = tweepy.OAuthHandler(API_KEY, API_SECRET)
    auth.set_access_token(ACCESS_TOKEN, ACCESS_SECRET)
    #Get the API instance
    api = tweepy.API(auth) # create an API object
    return api
``` 

This above function will call Twitter api with keys and secrets and will return an instance of OAuthHandler which we will use to post a tweet in next step.

Finally lets create tweet_image() function that we discussed above to download an image from Nasa api and tweet that image.


```
# Tweet an image with status and hashtag
def tweet_image(url,message):
    api = twitter_api()
    filename = 'temp.jpg'
	request = requests.get(url, stream=True)
	if request.status_code == 200:
		with open(filename, 'wb') as image:
            for chunk in request:
                image.write(chunk)
        api.update_with_media(filename, status=message)
        os.remove(filename)
    else:
        print("Unable to download image")
``` 

In above function, first we are calling twitter_api() function to get the OAuthHandler instance, then we are downloading image using Nasa's open api and finally we are calling update_with_media() function to tweet the image. Once we tweet the image we are deleting the image from our local directory using os.remove() function. That's it! our Nasabot is ready 😃

Our final nasabot.py should look like this
 

```
import tweepy,request,os
from keys import *


def twitter_api():
    # Lets authenticate our app with OAuth
    auth = tweepy.OAuthHandler(API_KEY, API_SECRET)
    auth.set_access_token(ACCESS_TOKEN, ACCESS_SECRET)
    # Get the API instance
    api = tweepy.API(auth)  # create an API object
    return api


# Tweet an image with status and hashtag
def tweet_image(url, message):
    api = twitter_api()
    filename = 'temp.jpg'
    request = requests.get(url, stream=True)
    if request.status_code == 200:
        with open(filename, 'wb') as image:
            for chunk in request:
                image.write(chunk)
        api.update_with_media(filename, status=message)
        os.remove(filename)
    else:
        print("Unable to download image")


if __name__ == "__main__":

    # NASA Image API access
    HOST = 'https://api.nasa.gov/planetary/apod?api_key=' + NASA_API_KEY
    nasa_obj = requests.get(HOST)

    if 'copyright' in nasa_obj.json():
        message = 'Astronomy Picture of the Day: ' + \
            nasa_obj.json()['title'] + ' copyright : ' + \
            nasa_obj.json()['copyright'] + ' #APOD'
    else:
        message = 'Astronomy Picture of the Day: ' + \
            nasa_obj.json()['title'] + ' #APOD'

    if 'url' in nasa_obj.json():
        url = nasa_obj.json()['url']
    else:
        url = nasa_obj.json()['hdurl']
    if nasa_obj.json()['media_type'] == 'image':
        tweet_image(url, message)
``` 

### Conclusion

Congratulations! with just few lines of code, we developed a Twitter bot that can tweet an image from Nasa's APOD server.  I am using cron job to run this code every day that downloads and tweets one astronomy picture of the day. Don't forget to check this bot with handle name  [@Nasarobot](https://twitter.com/nasarobot).








