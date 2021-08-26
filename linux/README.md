## linux
### 常用命令
### 运维常用命令收集
#### 查看磁盘情况
df -lh
#### 查找/usr目录下文件大小大于100MB的文件，并按照文件大小由大到小排列，并显示文件大小
find / -type f -size +100M | xargs ls -lh -S
#### 查看物理CPU个数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
#### 杀死全部php进程
ps -ef | grep php | grep -v grep | awk '{print $2}' | xargs kill -s 9
#### 统计IP访问量
awk '{print $1}' access.log | sort -n | uniq | wc -l
#### 查看访问最频繁的前100个IP
awk '{print $1}' access.log | sort -n |uniq -c | sort -rn | head -n 100
#### 查看访问100次以上的IP
awk '{print $1}' access.log | sort -n |uniq -c |awk '{if($1 >100) print $0}'|sort -rn

参考链接：https://www.huaweicloud.com/articles/27a1172e6c9e7bab6555fda4fb0d7825.html