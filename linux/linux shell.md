# ����kill����

ps -ef |grep hello |awk '{print $2}'|xargs kill -9

# grep�쳣Binary file a.log matches

���� grep "xxx" a.log

��� Binary file a.log matches

ԭ��grep��Ϊa.log�Ƕ������ļ�

���������grep -a "xxx" a.log