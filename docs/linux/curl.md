### curl

1. 访问

   ```
   curl url
   ```

2. 下载页面

   ```
   curl url>page.html
   ```

3. porxy

   ```
   curl -x proxy.url:port -o page.html url
   ```

4. cookies

   ```
   curl -x proxy:port -o page.html -D cookies.txt url
   ```

5. 显示过程

   ```
   curl --help
   curl -v url 
   curl -i url 显示头信息
   curl -L 自动跳转
   curl -u【--user】 username:password url  http 认证
   curl --user-agent "" url 模拟客户端操作
   curl --trace output.txt url 查看更详细的过程存储到文件
   curl --trace-ascii output.txt url
   ```

6. post

   ```
   -H header -d【--data】 参数
   curl url -X PUT/DELETE/POST -H "Content-Type:application/json;charset=utf8" -d '"title":"xxx"'
   ```

7. file

   ```
   curl url -F "file=@xxx" -F '{"xx":"xx"}'
   ```

8. &

   ```
   linux 需要对 & 转义 \&
   ```

   