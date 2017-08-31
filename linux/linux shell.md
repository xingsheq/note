# 批量kill进程

ps -ef |grep hello |awk '{print $2}'|xargs kill -9

# grep异常Binary file a.log matches

操作 grep "xxx" a.log

结果 Binary file a.log matches

原因：grep认为a.log是二进制文件

解决方法：grep -a "xxx" a.log