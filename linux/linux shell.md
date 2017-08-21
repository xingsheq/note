批量kill进程
ps -ef |grep hello |awk '{print $2}'|xargs kill -9