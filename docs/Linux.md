# iLinux

1. ### grep

   ```
   grep 【-v 不包含 -i 忽略大小写】 abc xxx.log 
   ```

2. ### tail

   ```
   tail -f file 监听文件变化
   tail -n xxx file 输出文件的倒数第 xxx 行
   ```

3. ### top

   ```
   top  P cpu 排序 
   top -p 进程号
   ```

4. ### lsof

   ```
   列出当前系统打开文件，访问网络连接
   lsof -i:port
   ```

5. ### nc

   ```
   探测端口
   扫描 ip:port 的 tcp 连接
   nc -vz ip port
   扫描 ip:port 的 udp 连接
   nc -uz ip port
   nc -vz www.baidu.com 443
   ```

6. ### netstat

   ```
   显示 ip tcp udp icmp 统计数据,检验端口网络连接
   netstat -t | -u
   ```

7. ### 磁盘占用

   ```
   du -sh * | sort -nr 当前目录
   lsof | grep delete 查看当前系统没有删除未释放的文件描述符
   ```

8. ### 查找

   ```
   find 查找目录 -name \*.后缀 文件
   find / -name xxx -d 查找文件
   locate \*.log 执行前 locate updatedb
   ```

9. ### tar

   ```
   tar -zcvf pack.tar.gz pack/  #打包压缩为一个.gz格式的压缩包
   tar -jcvf pack.tar.bz2 pack/ #打包压缩为一个.bz2格式的压缩包
   tar -Jcvf pack.tar.xz pack/  #打包压缩为一个.xz格式的压缩包
   ```

   ```
   tar -zxvf pack.tar.gz /pack  #解包解压.gz格式的压缩包到pack文件夹
   tar -jxvf pack.tar.bz2 /pack #解包解压.bz2格式的压缩包到pack文件夹
   tar -Jxvf pack.tar.xz /pack  #解包解压.xz格式的压缩包到pack文件夹
   ```

   