<center>Linux 指令集</center>

```she
# 更新 apt-get
sudo apt-get update

# apt-get 安装软件
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

# curl -fsSL: (来源于lio_zero)
# -f（--fail） — 表示在服务器错误时，阻止一个返回的表示错误原因的 HTML 页面，而由 curl 命令返回一个错误码 22 来提示错误。
# -L（--location） — 参数会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。
# -S（--show-error） — 指定只输出错误信息，通常与 -s 一起使用。
# -s（--silent） — 不显示错误和进度信息。

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
```

