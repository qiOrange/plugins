# PowerShell：因为在此系统上禁止运行脚本，解决方法

**get-executionpolicy**

若是 Restricted

则以管理员身份打开PowerShell 输入 **set-executionpolicy remotesigned**

选择Y 然后电脑上就可以执行自己编写的脚本文件