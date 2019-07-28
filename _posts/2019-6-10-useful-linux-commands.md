---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Useful Linux commands”
tags: [linux]
img: manhattan.jpg
date: 2019-06-10 12:00:00
---



### Part 1: ssh connection
1. connect using username and password authentication: **ssh username@clould_ip**
2. connect using key files:
   1.  **cd [keydir]**
   2.  **chmod 400 keyfilename.pem**
   3.  **ssh -i "keyfilename.pem" username@cloud_ip**


### Part 2: transfer file
1. upload files from local machine to cloud machine: **scp [local dir] username@cloud_ip:[cloud dir]**
2. download files from cloud machine to local machine **scp username@cloud_ip:[cloud dir] [local_dir]**
3. upload for aws: **scp -i [path to keyfile] [local dir] username@cloud_ip:[cloud dir]**
4. download for aws: **scp -i [path to keyfile]username@cloud_ip:[cloud dir] [local_dir]**
5. Copy/Sync Files and Directory to or From a Server : **rsync -avz exampledir/ root@ip:/home/**
6. change permission of certain file: **chmod -R ugo+rw file** 


### Part 3: check info of files and directory
1. count how many files in current dir: **ls -l $$\mid$$ grep -v ^l $$\mid$$ wc -l**
2. check if a certain file exist in dir: **ls x.txt && echo yes $$\mid$$ $$\mid$$ echo no**
3. check file/dir size: 
   1. check dir size: **du -sh [path to dir]**
   2. check file size: **du -h [path to file]**
4. count file in dir and its subdir : **find . -type f $$\mid$$ wc -l**
5. check how much space left on disk: **df -h**
6. check file detailed info of a file : **stat file**


### Part 4: vim for editting codes:
1.  undo: **u**
2.  move cursor by words: **w**


### Part 5: using git
1. add all: git add *
2. undo add all: **git reset**


### Part 6: tensorboard 
1.  **tensorboard --logdir='./logs' --port=6006**


### Part 7: check cpu info
1. show cpu architecture info: **lscpu**
2. check how many cores: **echo "Threads/core: $(nproc --all)"**


### Part 8: runnning command in Backstage
1. log system print info to log file: **nohup python3 -u example.py > cmd.log &**


### Part 9: Maven
1. compile java project: **mvn compile**
2. run java file: **mvn exec:java -Dexec.mainClass="lucene.search.Mutation""**

