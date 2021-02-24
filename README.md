# batchping
Batchping Project

# Linux

paping 命令  

之前写过一篇批量telnet的文章，记录下之前做过的批量Telnet命令，但是那个效率感觉有点低，然后在有几百上千的机器时候如果有机器不通会很慢，遂有了这一篇的使用paping来实现批量Telnet端口是否通的操作。
paping下载地址  
wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/paping/paping_1.5.5_x86_linux.tar.gz  

具体脚本如下，还是一样需要配置个ip.lst文件，然后弄个脚本，成功的是ping_ok.list,失败是ping_fail.lst，我这是最简单的，端口定义死了，改改ip.lst 和脚本，可以ping不同端口，重点这个比较快，而且paping直接上传到服务器上就可以用：  

```  
#!/bin/bash 
#功能，使用paping来进行批量端口检测，将端口通的和不通的分别写入不同文件内。

#初始化文件
echo "" >list.log
echo "" >ping_fail.lst
echo "" >ping_ok.lst

#循环处理文件 ip_a ,这是批量检查9300端口，可以自己改成其他的，或者列入检查文件里边也可以。
for ip in `cat ip.lst`
do 
  #检查两次，一次写入文件中,一次用于判断结果，如果响应是Failed = 1就是失败的。
  paping $ip -p 22-c 1 -t 100  >>list.log  2>&1
  ping=`paping $ip -p 22-c 1 -t 100 |grep Connected |awk -F '=' '{print $4}' |awk '{print $1}'`  
  if [ $ping -eq 1 ];then 
    echo $ip >>ping_fail.lst
  else
    echo $ip >>ping_ok.lst
  fi
done 
```  

# Windows  

## ICMP PING  

```  
echo                    ***** by hotsnow *****
echo         ***** 欢迎讨论，建议，修改，谢谢使用！*****
del /f ip_timeout.txt
echo.
echo ping start ...
echo send 1 packets ,time out 1 second
echo.
for /f %%i in (ip.txt) do ping -n 1 -w 1000 %%i |find "out" >nul && echo %%i && echo %%i >>ip_timeout.txt
echo 以上的 ip 不能 ping 通，列表保存在 ip_timeout.txt
echo.
echo === 需要查找资产信息请按任意键，结束请关闭窗口 ===
pause
```  

## TCP Port Ping  

```  
echo                    ***** by hotsnow *****
echo         ***** 欢迎讨论，建议，修改，谢谢使用！*****
del /f ip_timeout.txt
echo.
echo ping start ...
echo send 2 packets ,time out 1 second
echo.
for /f %%i in (ip.txt) do psping -n 1 -w 0 %%i 3389 2>nul |find "Received = 0" >nul && echo %%i && echo %%i >>ip_timeout.txt
echo 以上的 ip 不能 ping 通，列表保存在 ip_timeout.txt
echo.
echo === 需要查找资产信息请按任意键，结束请关闭窗口 ===
pause
```  


Windows ping ref: https://sites.google.com/site/laiweiwei/hotsnow/windows-bat/test-2  
Linux ping ref: https://blog.csdn.net/weixin_42488171/article/details/96569695  
更改Batch编码: https://blog.csdn.net/u012815136/article/details/101549751  
