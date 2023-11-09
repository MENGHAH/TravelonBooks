# C盘占用过高问题

如果C盘占用过高，可以考虑使用mklink构建连接对C盘中比较大的文件夹进行迁移

以vscode中占用C盘比较多的两个文件为例进行说明：

1. 在D盘创建目标文件夹VSCodeConfig

2. 将C盘的.vscode和Code剪切到D盘VSCodeConfig

3. 创建连接

   ```shell
   mklink /d "C:\Users\username\.vscode" "D:\VSCodeConfig\.vscode"
   mklink /d "C:\Users\username\AppData\Roaming\Code" "D:\VSCodeConfig\Code" 
   ```