fangyijoe.github.io
===================

Joe's blog


1.首先管理员权限启动cmd命令提示符


点开始，搜索中输入CMD，在上面右键以管理员权限运行


2.启动BCDEDIT


提示符下输入这个bcdedit查看设置


也可以直接第三步


3.输入命令关闭bootmgr


很简单，就一句：


BCDEDIT -SET {BOOTMGR} displaybootmenu NO
