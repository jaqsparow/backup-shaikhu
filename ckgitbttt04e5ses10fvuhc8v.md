## How to access Google Sheets using Python and gspread

Hello everyone, In this post I am going to show how we can access and update Google spreadsheet using Python and an excellent library **gspread**. With just few lines of codes we can create a new or use existing sheet and do the operation such as add, 
update or delete. Another cool thing is you can convert this spreadsheet into pandas dataframe using just one line. So lets get started..

### Prerequisite
- Google developer account to create api credentials
- Google service account to access spreadsheet programmatically
- gspread - Python library
- oauth2client - Python library 

### Create Google service account

**Step 1: **First you need to signup for a Google developer account if you have not already. Visit Google developer signup page at  [https://console.developers.google.com/](https://console.developers.google.com/) to create your account.

**Step 2: Create service account**
Since we will be accessing spreadsheet using programs, we need to create service account and credentials for it.  So login to your developer account and create new project by clicking at the top left corner new project icon.

As shown below a new window will appear. Provide your project name and click create button

![img1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603219492785/6aUnnXGrg.png)

Now once project is created, it will take you to the project page (If not click on small bell icon on the top right and go to the project page). It should look like below screen print

![img2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603219644004/5_wjZgUML.png)

Now we need to enable APIs that we want to use. For this tutorial we need to enable api for Google drive and Google sheet. We need google drive api because your spreadsheet is going to be saved on your google drive. 
So click menu icon on top left corner and then go to '**APIs and Services**' and then click **Library** and it will take you to the API Library page like below

![img3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603219949776/isSLOy9_J.png)

Now search for Google Drive api and click enable 

![img4.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220002661/jDAa3rVja.png)

Once enabled it will take you to Google drive api page. Now click **create credentials**

![img5.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220070558/sYwIgZY7X.png)

To create credentials follow the steps as shown in below screenshot and click "**what credentials do I need?**"


![img6.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220135393/UKnU5zR91.png)

In the next page you need to provide service account name, service account id and role as editor since we want to do add and update operation on Google sheet programmatically.

![img7.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220378401/Ut_7thcLL.png)

When you click continue in above step, a JSON file with authentication details will be created.

![img8.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220458737/TEalm8X-6.png)

Save this file in your system.

At this point we have created the service account successfully. Now lets enable Google Sheet api using the same steps we used to enable Google drive api. 

![img4a.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220567895/0J6YnwSHL.png)

Congratulations!  we have successfully created our service account to access Google spreadsheet using Python. Now lets create a sample spreadsheet so that we can use it for our Python program to access. Lets go to  [https://docs.google.com/spreadsheets](https://docs.google.com/spreadsheets)  and create a sample sheet. I have create a sheet named Student for this example tutorial.

![img10.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220809508/wz5lZcvkX.png)

To use this sheet with our python code we need to share the file with Google api client email ID. Open a spreadsheet and click **Share** button on top right corner of the spreadsheet. You can find the client email from the JSON file we just downloaded in the previous example.

![img9.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603220910385/WZ7K99SKh.png)

### Install Python libraries
Install these libraries so that we can add and update spreadsheet using python


```
pip install gspread
pip install oauth2client
```
Though these two libraries are enough for us to for this tutorial. However we can install pandas to view our data using Pandas's Dataframe

```
pip install pandas as pd
``` 


### Lets start coding
At this point we have created Google account and then enabled Google drive api and Google sheet api and created credentials and saved in our local drive as JSON file. We also created a sample spreadsheet and shared the sheet with client email provided in the JSON file. We also installed required Python libraries in the above step. So now lets get started to code 🙂


```
import gspread
from oauth2client.service_account import ServiceAccountCredentials
import pandas as pd
``` 
With above code we imported the libraries we installed in the last step. Now before accessing the spreadsheet we need to authenticate using JSON file we downloaded


```
#Authenticate Google service account
gp = gspread.service_account(filename='Testapp-9f185f872dd9.json')
``` 
We need to provide the correct path of JSON file. Since I have copied this file to my project directory, so just name is enough.

Now lets open the spreadsheet 'Student' we created in one of the steps.
```
#Open Google spreadsheet
gsheet = gp.open('Student')
``` 
Since spreadsheet is opened, now we need to select the worksheet we want to access using below line


```
#Select worksheet
wsheet = gsheet.worksheet("Sheet1")
``` 
As shown above we have selected 'Sheet1' which is the first worksheet.

### Retrieve all records from a worksheet

Now to retrieve the records or cell values we can use one of below functions

- **get_all_values()** fetches all values from a worksheet as a list of lists.
- **get_all_records()** fetches all values from a worksheet as a list of dictionaries.

Lets try one by one on Jupyter Notebook
```
wsheet.get_all_values()
``` 
Output of above statement on Jupyter notebook. As shown all records are fetched as list of dictionaries 

```
[['Student', 'Course', 'Grade', 'Score'],
 ['John', 'Data Science', 'A', '480'],
 ['Amit', 'Cyber Security', 'B', '440'],
 ['Ronit', 'Data Science', 'A', '475'],
 ['Maya', 'Business Analytics', 'A', '481']]
``` 

Similarly for get_all_values()


```
wsheet.get_all_records()
``` 
Output:

```
[{'Student': 'John', 'Course': 'Data Science', 'Grade': 'A', 'Score': 480},
 {'Student': 'Amit', 'Course': 'Cyber Security', 'Grade': 'B', 'Score': 440},
 {'Student': 'Ronit', 'Course': 'Data Science', 'Grade': 'A', 'Score': 475},
 {'Student': 'Maya',
  'Course': 'Business Analytics',
  'Grade': 'A',
  'Score': 481}]
``` 

Using Pandas DataFrame. First we extracted all values from worksheet, then set the columns for dataframe which is nothing but first list of all_rows.


```
all_rows = wsheet.get_all_values()
columns = all_rows.pop(0)
df = pd.DataFrame(all_rows,columns=columns)
``` 
Here is the output of df

```
	Student	Course	Grade	Score
0	John	Data Science	A	480
1	Amit	Cyber Security	B	440
2	Ronit	Data Science	A	475
3	Maya	Business Analytics	A	481
``` 

### Retrieve cell values from a worksheet

To get a specific cell value use **acell** function. Below snippet will print "John"

```
cell_A2 = wsheet.acell('A2').value
print(cell_A2)
``` 
To get the value using coordinates use **cell ** function as shown below. This will also print "John"


```
cell = wsheet.cell(2,1).value
print(cell)
``` 
### Creating another worksheet

To create another sheet on the same spreadsheet use **add_worksheet** function


```
another_sheet = gsheet.add_worksheet(title="AnotherSheet", rows="10", cols="10")
``` 
This will create another sheet named "AnotherSheet" with 10 rows and 10 columns

### Deleting a worksheet

Use function **del_worksheet **to delete a worksheet. So below instruction will delete "AnotherSheet" we created just now in the last step

```
gsheet.del_worksheet(another_sheet)

``` 
### Updating cell value
To update cell values we can use **update **function like below
Below operation will replace "John" with "David"

```
wsheet.update('A2', 'David')
``` 
We can perform same operation using coordinates 


```
worksheet.update_cell(1, 2, 'David')

``` 
### Insert a new row

With **append_row**, we can append a new row at the end. As shown below, we are adding a new row for new student.


```
wsheet.append_row(["Gina","Computer Science",'B',447])
``` 

### Conclusion

As shown in above examples, **gspread ** is an excellent and super easy to use library to access Google spreadsheet. Hopefully examples and steps shown above  will help you build your apps using Google spreadsheet. 
I have been using **gspread ** for one of my app to track my daily expenses using sms. I am going to post about this in my next story on how to build an expense tracker using **gspread**,**Twilio **and **Flask** and then deploying it on Heroku 🙂

