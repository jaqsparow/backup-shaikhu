## Files, Folders and Python

In this post we are going to see how we can use Python to find the size of any file and folder. We will check how many files we have inside a folder and how many of them are empty files and how to delete those empty files. We can also check how old a file is and when was the last time file was updated and accessed. We are going to use Python's powerful module **os** for this. So lets start..

### How to get current working directory and how to change it?

First of all, lets import **os** module.
```
import os
```

To get the current working directory lets use **getcwd()** function
```
path = os.getcwd()
print(path)
```

> Output:  C:\Users\jaqsp\Desktop\other

#### How to change working directory?

Lets change the current working directory with **chdir()**
```
os.chdir('C:\\Users\\jaqsp\\Desktop')
```
Lets check the current directory once again

```
print(os.getcwd())
```
>Output C:\Users\jaqsp\Desktop

### How to get list of all files and folders inside a directory ?

To get the list of files and folders inside a directory, we can use function **scandir**. This function returns an object of **os.DireEntry**. This object represents file path and other file attributes such as

- **name** : file or directory name
- **path** : full path of the file
- **is_dir()** : This function returns True if the entry is a directory
- **is_file()** : This function returns True if the entry is a file

So lets use **scandir()** function as shown below

```
files = []
dirs = []
path = 'C:\\Users\\jaqsp\\Desktop\\other'
with os.scandir(path) as it:
    for file in it:
        if os.DirEntry.is_file(file):
            files.append(file.name)
        else:
            dirs.append(file.name)
print('Directories: ',dirs)
print('Files :',files)
```
>Output : Directories: ['.ipynb_checkpoints', 'expense', 'git', 'qual-id']

> files : ['exp.json', 'files.ipynb', 'Google spreadsheet.ipynb', 'gspread.ipynb', 'my_screenshot.png', 'OS.ipynb']

In the above code, we have created two empty lists files and dirs and then used **scandir()**  function with input path.
We have used **is_file()** and **is_dir()** to identify if the entry returned is file or a directory. Finally we appended the list using append function


### How to get file size ?

There are two ways to get the file size i.e. using **os.path.getsize()** function or using **os.stat()** function. The size returned by each function is in bytes
In below example we will get size of file *exp.json*
```
size = os.path.getsize('exp.json')
print(size)
```
> Output : 2313

As shown the file size is 2313 bytes.


### How to get file create, update and access date, owner name and size of file

We will use **os.stat(file)** function to get all the details about a file. This function returns below along with other details. We are not mentioning all the return attributes. Please check  this link for all the attributes [here](https://docs.python.org/3/library/os.html) 
-  **st_uid** : This attribute tells the user id of file owner
- **st_gid** : This is group id of file owner
- **st_size**: This is size of the file in bytes
- **st_atime**: Last accessed time in no of seconds
- **st_ctime**: File creation time in seconds
- **st_mtime**: last update/modified time in seconds

Now lets do an example
```
stat = os.stat('exp.json')
print(stat)
```
> 
os.stat_result(st_mode=33206, st_ino=24206847997197434, st_dev=4237007564, st_nlink=1, st_uid=0, st_gid=0, st_size=2313, st_atime=1603293698, st_mtime=1602796599, st_ctime=1602796598)

As shown above file size is 2313 bytes and file creation/modification times are in seconds

Lets convert the time in seconds to readable string
```
import time
time.ctime(stat.st_ctime)
```
> Output: 'Fri Oct 16 02:46:38 2020'
We have used **ctime** function to convert the time in seconds to string readable format

### How to get folder size and empty files

```
import os
from os.path import join, getsize
path = 'C:\\Users\\jaqsp\\Desktop\\other'
folder_size = 0
empty_files = []
all_files = []
for paths,dirs,files in os.walk(path):
    for file in files:
        filename = os.path.join(paths, file)
        file_size = os.path.getsize(filename)
        all_files.append(filename)
        if file_size == 0:
            empty_files.append(filename)
        folder_size += file_size
print('current folder size is',folder_size)
print('total files present: ',len(all_files))
print('Total empty files: ',len(empty_files))
#Print all the empty files
print('Below are empty files found')
for i in range(len(empty_files)):
    print(empty_files[i])        
```
Output:
```
current folder size is 578231
total files present:  230
Total empty files:  4
Below are empty files found
C:\Users\jaqsp\Desktop\other\qual-id\qual_id__init__.py
C:\Users\jaqsp\Desktop\other\qual-id\test__init__.py
C:\Users\jaqsp\Desktop\other\qual-id\test\categories__init__.py
C:\Users\jaqsp\Desktop\other\qual-id\test\utils__init__.py
```
 In the above example we have used Python's **walk(path)** function to list all the files inside the directory tree by walking either top-down or bottom-up
Then we used **join()** to join the path and file and return full path of the file and finally we use **getsize()** function to retrieve size of the file. Folder size in above example is 578231 bytes (564KB)

### How to remove a file and directory?
Deleting or removing a file or directory in Python is super easy but make sure you want to delete it as it cannot be recovered once deleted.

To remove or delete a file, Python's **os** module provides function **remove(path)**. If the input path is directory, it will through an exeception as this function is only to remove a file not a folder.

```
os.remove('abc.txt')
```
We can use **rmdir(path)** function to remove or delete a directory

```
os.rmdir('some_folder')
```

### Conclusion
Hope this post will help to understand how we can use Python's **OS** module to do daily activities related to files and directories. We learned how to get current directoy, how to change the directory and how to get different file attributes and also how we can delete a file and directory. Visit my GitHub page for code samples and don't forget to check my other posts.

#### References
-  [https://docs.python.org/3/library/os.html](https://docs.python.org/3/library/os.html) 

#### Check my other posts

-  [https://shaikhu.com/how-to-develop-a-twitter-bot-using-python-and-nasa-api](https://shaikhu.com/how-to-develop-a-twitter-bot-using-python-and-nasa-api) 

-  [https://shaikhu.com/how-to-keep-your-computer-awake-using-python](https://shaikhu.com/how-to-keep-your-computer-awake-using-python) 

-  [https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 

-  [https://shaikhu.com/how-to-access-google-sheets-using-python-and-gspread](https://shaikhu.com/how-to-access-google-sheets-using-python-and-gspread) 

-  [https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 


