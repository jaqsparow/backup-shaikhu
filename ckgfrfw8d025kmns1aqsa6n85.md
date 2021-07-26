## How to manage background processes in Linux

If you are new to Linux operating system and you start a command or process using terminal/session, you must have noticed that you need to wait till a process or command get finished before starting another command. This is because when you run a command using session or terminal, the process starts in the foreground by default. What should you do to run another command without opening a new terminal?
In this post I am going to show how you can manage background and foreground process in Linux. 
 
But before that lets understand what is foreground process and what is background process.

**Foreground process** is the process or job that is currently running on the terminal. So there will be only one foreground process per terminal.You need to wait till the current foreground process finishes before starting a new foreground process.
**Example: **Any command or process you start in the current session

**Background process** is the process or job running on the background and doesn't require interaction from the user. There can be more than one background process obviously.
**Example:** most common example is your web server.

For this example I am going to start command VMSTAT 1

```
shaikh@shaikhu-com:~$ vmstat 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 3045156 167484 2449380    0    0    80    56  469 1138 19  5 76  0  0
 1  0      0 3044644 167492 2450152    0    0     0    92  299  604  2  1 98  0  0
``` 
**vmstat** is command that displays real time memory usage and cpu utillization. If you want to know more about it visit my previous post  [here](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux-ckgcp9gex07g8pas1ees0hz7t) 

In the above command 
Things to note here is that above command will print CPU stats every five second on the terminal until you interrupt it. If you want to terminate it just press **CTL + C** or if you want to pause or stop, press **CTL + Z**

### bg: Move a job to background
If you want to move a job that is already started to background so that you can access the terminal press **CTL + Z ** and then **bg**

lets start a job in the foreground using below command. Here we are writing CPU stats on a text file. As you can see , we cannot start a new command as this is running on the foreground.
```
shaikh@shaikhu-com:~$ vmstat 5 >> vmstat.txt

``` 
Lets do **CTL + Z** to pause this job and then do **bg**


```
shaikh@shaikhu-com:~$ vmstat 5 >> vmstat.txt
^Z
[1]+  Stopped                 vmstat 5 >> vmstat.txt
shaikh@shaikhu-com:~$ bg
[1]+ vmstat 5 >> vmstat.txt &
shaikh@shaikhu-com:~$ 
```

Now the job running in the background and we got the terminal

### How to start command and run it in the background

To start a command and run it in the background use **&** as shown below
```
command &
```
example: 
```
shaikh@shaikhucom:~$ sleep 400 &
[2] 11122
shaikh@shaikhucom:~$ 
```

### jobs : command to check the job status
This command displays all the jobs running in the current terminal

```
shaikh@shaikhucom:~$ jobs
[1]-  Running                 vmstat 5 >> vmstat.txt &
[2]+  Running                 sleep 400 &
shaikh@shaikhucom:~$ 

``` 
Here number withing bracket is [n] is job id or job number and **+** indicates most recent command or job whereas **-** indicates previous job. If you want to see the process id use **-l** option
```
shaikh@shaikhucom:~$ jobs -l
[1]- 10216 Running                 vmstat 5 >> vmstat.txt &
[2]+ 11122 Running                 sleep 400 &
shaikh@shaikhucom:~$ 
```
The number 10216 and 11122 are process id

Different options for **jobs**
#####       -l :	lists process IDs in addition to the normal information
#####       -n : 	lists only processes that have changed status since the last
#####     		notification
#####       -p :	lists process IDs only
#####       -r :	restrict output to running jobs
#####       -s :	restrict output to stopped jobs

### kill %n: to kill a job with job id n

Kill command is used to kill a job. Note that **%** will be used to indicate job id or job number
```
haikh@shaikhu-com:~$ jobs
[1]-  Running                 vmstat 5 >> vmstat.txt &
[2]+  Running                 sleep 400 &
shaikh@shaikhucom:~$ kill %2
shaikh@shaikhucom:~$ jobs
[1]+  Running                 vmstat 5 >> vmstat.txt &
shaikh@shaikhucom:~$ 


```
 As you can see now we have only one job running in the background and notice the **+** sign which indicates the job id 1 becomes the most recent job 🙂

### fg : command to move a job to foreground
Use **fg **command to move a job to foreground. By default it will bring most recent background job to foreground

```
shaikh@shaikhucom:~$ jobs
[1]   Running                 vmstat 5 >> vmstat.txt &
[2]-  Running                 sleep 400 &
[3]+  Running                 sleep 500 &
shaikh@shaikhucom:~$ fg
sleep 500
[2]   Done                    sleep 400
shaikh@shaikhucom:~$ 

```

If you have more than one jobs running in the background then use **%n** to move a specific job to the foreground
```
shaikh@shaikhucom:~$ fg %2
```

### ps command to see all the processes

Use **ps** command to see active processes.
Use below options
##### ps ax : to see all the process currently active in the system (Use my previous post to check the commands for real time process). It will be a very long list, so use less/more parameters

##### ps T: list all processes running on current terminal

```
shaikh@shaikhucom:~$ ps T
    PID TTY      STAT   TIME COMMAND
   5786 pts/0    Ss     0:00 bash
  10216 pts/0    S      0:00 vmstat 5
  12983 pts/0    R+     0:00 ps T
shaikh@shaikhucom:~$ 
```
### Conclusion
So in this post we learned different commands to manage background and foreground process. We learned
##### - **bg** and **fg** to move a job to the background and foreground ##### respectively. 
##### - **jobs** command to list all the jobs active in the current terminal. 
##### - **kill** command to kill a job
##### - **ps** command to see list of all the active and running processes

##### We also learned how to start a job in the background using **&**. If you want to know real time memory and cpu usage and control all the processes running on your system please check my last post  [https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux-ckgcp9gex07g8pas1ees0hz7t](https://shaikhu.com/how-to-monitor-cpu-utilization-in-linux-ckgcp9gex07g8pas1ees0hz7t) 










































