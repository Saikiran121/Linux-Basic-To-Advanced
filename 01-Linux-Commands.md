# 01. You're managing a web appication thats been running for a while. and it generated various log files everyday and our task is to investigate error messages that users has been reporting?
- we have an server where our applications are running so we first check what all files we have within our web application folder
  ls  - Lists the contents of a directory. it shows what files and folders you have  

- we can also list a files and folders in an "log" folder
  ls logs

- Now i want to check files and folders in an "log" folder and in log folder if i have multiple folders i want to see the contents inside those folders also so here we use
   ls -R 

- if i want to list folders inside the "log" folder
    find . -type d