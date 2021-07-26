## What is fork() system call and how to fork using Python

Before going into fork, lets understand what is process.  A **process** in computer term is a program being executed currently by a computer. Each process is unique and can be identified by its PID or process ID.
- Note: All the examples and demo codes shown below were tried on Ubuntu 20.04 LTS and Python v3.8.5.

Visit my Github page for all the demo code snippets  [https://github.com/jaqsparow/fork-demos](https://github.com/jaqsparow/fork-demos) 
#### What we will learn in this post?
- What is fork 💡
- How to call fork in python 📗
- How to get process id or PID in Python 📙 
- How to identify parent and child process 📕
- Examples with code snippets 💻 


### Introduction : What is fork system call?
Fork is one of the most important concept in Unix and Linux operating system. In short note, fork is nothing but cloning a process. That means fork will create a new process with exact copy of calling process. So when a program encounters a fork() system call, it will create another process with same copy of memory. So here comes the concept of parent and child process.

The main or first process which calls fork and creates a new process is called **parent process**. The new process created by fork is known as **child process**.

### How to identify parent and child process?
Since both processes child and parent have exact same copy of memory, then comes the question how can we identify which of them is parent and which one is child. As I mentioned above, each process has unique ID known as process ID or PID which can be used to differentiate among processes. 

To identify parent and child process, we need to check the return code of fork system call. 

#### Return code of fork()
Return code of fork system call determines the parent or child process.
When the parent process calls fork, fork returns ** PID of child process just created** to parent process and **0 **to child process. So basically if return code from fork call is zero, then its child process and if its a positive value, then it must be the parent process.

- **ZERO** If return code is 0, then it must be the child process
- **A positive value** , If return code is positive value (or child's PID), then its parent process
- **Negative **, If return code is negative, then child process creation is failed and unsuccessful


![fork.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603476927418/IDJMNLLyf.png)

### How to fork using Python?

Python's **os** module provides a function **fork()** to create a child process. To know the PID of any process, use function **getpid()** from **os** module


```
import os
os.fork()
``` 
Now lets do some demos to understand whats going on

#### DEMO 1: To check process ID of any process
In below example we are just checking how getpid() can be used to get the PID of current process.

```
import os

def demo1():
    print('I am the only process, My PID:',os.getpid())
demo1()
``` 
Here is the output:

![demo1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1603465824199/6lUSzeXhS.png)

#### DEMO 2: Create one  child process using fork()

In below example we are printing process ID before and after fork() call. That means before fork, we have one process and after call we got another new process with total of 2 processes. 

Lets check the output of below snippets
```
import os

def demo2():
    print('Before calling fork(),PID: ', os.getpid())
    os.fork()
    print('After calling fork(), PID: ', os.getpid())
    
demo2()
``` 
Here goes output: -

```
shaikh@ubuntu:~/Jupyter/fork demos$ python3 demo2.py
Before calling fork(),PID:  6837
After calling fork(), PID:  6837
After calling fork(), PID:  6838
shaikh@ubuntu:~/Jupyter/fork demos$ 
```
As displayed above, before fork() we only had one process with PID 6837 and after fork we have a new process with PID 6838. 

#### Demo 3: To identify parent and child

Lets see, how we can identify parent and child programmatically. As mentioned in the last section, that if return code from fork is zero, then its child process and if its a positive value then its parent process. Lets check the same here

```
import os

def demo3():
    print('Before calling fork(),PID: ', os.getpid())
    rc = os.fork()
    if rc == 0:
        print('I am child, PID: ', os.getpid())
        os._exit(0)
    elif rc > 0:
        print('I am parent,PID:',os.getpid())
    else:
        print('Child process creation failed!!')
    
demo3()
```

**Output**
```
shaikh@ubuntu:~/Jupyter/fork demos$ python3 demo3.py
Before calling fork(),PID:  7316
I am parent,PID: 7316
I am child, PID:  7317
```

Lets understand what happened above. Before fork we had only one process with PID 7316 and the moment it called fork(), we got another process. Each of those processes have different copy of return code **rc**. The parent has rc with positive value (PID of child process) and child has the rc equals to **zero**

#### Demo 4: Lets create two child process
In below example, we are calling fork() twice. 

```
import os

def demo4():
    #No fork, only one process
    print('Before any fork,  PID:',os.getpid())
    #First fork
    os.fork()
    print('After first fork, PID:',os.getpid())
    #Second fork
    os.fork()
    print('After second fork,PID:',os.getpid())

demo4()
```
Here goes the output:

```
shaikh@ubuntu:~/Jupyter/fork demos$ python3 demo4.py
Before any fork,  PID: 7471
After first fork, PID: 7471
After first fork, PID: 7472
After second fork,PID: 7471
After second fork,PID: 7473
After second fork,PID: 7472
After second fork,PID: 7474
```

- Before first fork there was only one process
- After first fork, total processes  are two 
- After second call, total processes are four


> If we call fork 3 times, guess the number of processes that will be created in the comment section 🙂

#### Demo 5: Lets do it for fun
Below example will show that after fork call both parent and child will have different copies of variable **num**

```
import os

def demo5():
    num = 0
    rc = os.fork()
    if rc > 0:
        num = os.getpid()
    print ('num: ',num)    
demo5()
```
And guess the output 🙂

```
shaikh@ubuntu:~/Jupyter/fork demos$ python3 demo5.py
num:  7825
num:  0
```
So in above code, only parent process can go inside **if** statement because it has the positive response code which is PID of child process. Since **rc** for child is **ZERO**, it will still have the original copy of **num** 

### Conclusion
Hope this was fun and interesting learning 🙂. fork is very common way to create a child process in any Linux operating system. It is being used to create multiple processes and the most common use case is web server that forks a new process on each http request.

Be careful when you use fork and make sure you have exited the process successfully after completion of a task else there will be high memory and cpu usage and may create memory leak situation which is called **fork bomb**

All the demos are in my GitHub page.  [Click here to visit](https://github.com/jaqsparow/fork-demos) 

#### References

1.  [http://www2.cs.uregina.ca/~hamilton/courses/330/notes/unix/fork/fork.html](http://www2.cs.uregina.ca/~hamilton/courses/330/notes/unix/fork/fork.html) 


2.  [http://people.cs.pitt.edu/~aus/cs449/ts-lecture14.pdf](http://people.cs.pitt.edu/~aus/cs449/ts-lecture14.pdf) 


#### Similar posts

-  How to manage processes in Linux [All about process](https://shaikhu.com/how-to-manage-background-processes-in-linux) 
- How to monitor CPU utilization in Linux  [cpu utilization](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux) 
- How to schedule jobs using crontab  [How to use crontab](https://shaikhu.com/how-to-schedule-and-manage-tasks-using-crontab) 












