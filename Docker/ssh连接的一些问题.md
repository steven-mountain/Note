```bash
# 如果使用 
ssh username@ip port
# 这会出错，known_hosts文件有问题

# 推荐使用
ssh -p port username@ip
# 这样不会出错

# known_hosts 文件位置
# ~/.ssh/known_hosts

```



