# Crontab Powershell #

Crontab is a built in function in Linux machines that allows us to specify when we want programs to be executed. Usually when we are working on a Linux machine we use Powershell, so all the commands are in **bash**. The see a list of crontab commands:
```
crontab -l 
```
To edit the crontab commnads in the Linux machine:

```
crontab -e
```
There are a number of ways you can visualise the crontab commands. The default visualiser is nano. To change the visualisation type:

```
export VISUAL=vim
export EDITOR=vim
```
Nano is very straightforward to naivgate, vim is a littler harder:

- From here you then need to click "i" on the keyboard to edit the file

- Then when you have finished editing the file click esc and then 

```
:wp
```
- : = to instigate the command, w = write and q = quit

When writing Crontab commands there is a certain structure:

```
* * * * * python3 /home/azureuser/test_script_4.py
```

The first 5 stars represent the time in which you want to execute the command. Most information in this can be found in thie [link](https://crontab.guru/). Thre rest of the line indicates the language and the location of the file.
```
m h d m y <command/coding language> <location of the python file>
```


In order to kill a crontab programme type the following command into powershell:
```
sudo service cron reload
```