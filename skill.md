# 计算机使用技巧
## linux相关
ubuntu 中英文切换
vim 打开/etc/default/locale 
然后修改为	
LANG=”en_US.UTF-8″
LANGUAGE=”en_US:en”

重启即可

centos中英文切换
执行命令vi locale.conf打开并配置locale.conf文件。
只需要把locale.conf文件中的LANG="zh_CN.UTF-8"修改成LANG="en_US.UTF-8"即可，
也可以直接注释掉中文语言，即#LANG="zh_CN.UTF-8"。
