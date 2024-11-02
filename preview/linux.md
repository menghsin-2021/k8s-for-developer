# 基本的 Linux 操作能力

## [cd（change directory，切換目錄）](https://man7.org/linux/man-pages/man1/cd.1p.html)

```bash
# 通常都是 cd 後面接續一個相對或是絕對路徑
ubuntu@my-server:~$ cd [相對路徑或絕對路徑]

# 預設 ~ 就是回到家目錄，我們這個使用者是 ubuntu 所以 cd ~ 會回到 /home/ubuntu 目錄
ubuntu@my-server:~$ cd ~

# 沒有加上任何路徑，還是代表回到自己家目錄的意思
ubuntu@my-server:~$ cd

# .. 表示去到目前的上層目錄，亦即是 /home 的上層目錄的意思
ubuntu@my-server:~$ cd ..
ubuntu@my-server:/home$

# - 表示回到剛剛的那個目錄，也就是 //home/ubuntu
ubuntu@my-server:/home$ cd -

# 此方式為絕對路徑的寫法，直接指定要去的完整路徑名稱。此範例為 Nginx 設定檔目錄路徑
ubuntu@my-server:~$ cd /etc/nginx/conf.d/
ubuntu@my-server:/etc/nginx/conf.d$
```

## [mkdir（make directories）](https://man7.org/linux/man-pages/man1/mkdir.1.html)

```bash
# 建立一個 demo 的目錄
ubuntu@my-server:~$ mkdir demo
ubuntu@my-server:~$ ls
demo
```

## [ls（list directory contents）](https://man7.org/linux/man-pages/man1/ls.1.html)

```bash
# 檢視當前目錄有哪些檔案
ubuntu@my-server:/etc/nginx/conf.d$ ls
default.conf

# 帶入參數，-a：全部的檔案，連同隱藏檔（開頭為 . 的檔案）一起列出來；-l：長資料串列出，包含檔案的屬性與權限等等資料。
ubuntu@my-server:/etc/nginx/conf.d$ ls -al
total 12
drwxr-xr-x 2 root root 4096 Jan  2  2024 .
drwxr-xr-x 5 root root 4096 Oct  4 14:44 ..
-rw-r--r-- 1 root root 2004 Jun 14  2022 default.conf
```

## 預覽檔案的方式 [cat（concatenate）](https://www.man7.org/linux/man-pages/man1/cat.1.html), [head](https://man7.org/linux/man-pages/man1/head.1.html), [tail](https://man7.org/linux/man-pages/man1/tail.1.html), [less](https://man7.org/linux/man-pages/man1/less.1.html)

```bash
# 檢視全部預覽檔案
ubuntu@my-server:/etc/nginx/conf.d$ cat default.conf
server {
    server_name  server.example.com;

    access_log  /var/log/nginx/server.example.com_access.log  main;

    # location / {
    #     root   /usr/share/nginx/html;
    #     index  index.html index.htm;
    # }
    location / {
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Proto $scheme;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header Host $http_host;
         proxy_pass http://127.0.0.1:2368;
     }
...
... 以下省略
```

```bash
# head 預設僅會檢視檔案前 10 行
ubuntu@my-server:/etc/nginx/conf.d$ head default.conf
server {
    server_name  server.example.com;

    access_log  /var/log/nginx/server.example.com_access.log  main;

    # location / {
    #     root   /usr/share/nginx/html;
    #     index  index.html index.htm;
    # }
    location / {
```

```bash
# tail 預設僅會檢視檔案前 10 行
ubuntu@my-server:/etc/nginx/conf.d$ tail default.conf

server {
    if ($host = server.example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen       80;
    server_name  server.example.com;
    return 404; # managed by Certbot
}
```

```bash
# less 提供一頁一頁的顯示檔案內容，並能往前翻頁
ubuntu@my-server:/etc/nginx/conf.d$ less default.conf
```

## [grep - print lines that match patterns](https://man7.org/linux/man-pages/man1/grep.1.html)

```bash
# 過濾 Nginx default.conf 檔案符合 "server" 的內容
ubuntu@my-server:/etc/nginx/conf.d$ ls
default.conf
ubuntu@my-server:/etc/nginx/conf.d$ grep "server" default.conf
server {
    server_name  server.example.com;
    access_log  /var/log/nginx/server.example.com_access.log  main;
    # redirect server error pages to the static page /50x.html
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    ssl_certificate /etc/letsencrypt/live/server.example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/server.example.com/privkey.pem; # managed by Certbot
server {
    if ($host = server.example.com) {
    server_name  server.example.com;
```

## [base64（base64 encode/decode data and print to standard output）](https://linux.die.net/man/1/base64)

```bash
# 將 hello 以 base64 encode
ubuntu@my-server:~$ echo "hello" | base64
aGVsbG8K

# 將 aGVsbG8K 以 base64 decode
ubuntu@my-server:~$ echo "aGVsbG8K" | base64 -d
hello
```

## [env（run a program in a modified environment）](https://man7.org/linux/man-pages/man1/env.1.html)

```bash
# 印出目前的 shell 環境下的所有環境變數與其內容
ubuntu@my-server:~$ env
SHELL=/bin/bash
BYOBU_PYTHON=python3
BYOBU_HIGHLIGHT=#DD4814
TMUX=/tmp/tmux-1000/default,1543272,1
LESS_TERMCAP_se=
LESS_TERMCAP_so=
... 下略環境變數
...
```

## 參考

* 鳥哥私房菜 - 第六章、Linux 檔案與目錄管理 - https://linux.vbird.org/linux_basic/centos7/0220filemanager.php
