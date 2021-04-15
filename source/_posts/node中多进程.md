---
title: node中多进程
date: 2021-04-15 09:33:27
tags: nodejs
---

##### 实现背景

node 中单进程，无法利用多核CPU, 解决办法，就是一个核跑一个进程

##### 实现方法

###### 使用child-process 模块

<!--more-->

1. exec exec可以用操作系统原生的方式执行各种命令，如管道 cat ab.txt | grep hello

   ```
   const childProcess=require("child_process");
   childProcess.exec("cat /Users/sogubaby/Desktop/demo.txt",function(err,msg,srderr){
     console.log("error",err);
     console.log("res",msg);
     console.log("strout",srderr);
   })
   
   输出结果
   sogubaby ~/Desktop/learn_node/manyprocess$node child-process.js 
   error null
   res yedong
   yedong
   
   strout 
   
   err:    执行中遇到的错误
   msg:    正确执行的结果
   strout  执行错误后终端中输出的结果
   ```

2. execFile 执行一个文件

3. spawn

4. fork