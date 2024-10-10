 

#### 1\. [CURL](https://so.csdn.net/so/search?q=CURL&spm=1001.2101.3001.7020)命令简介

CURL（CommandLine Uniform Resource Locator），是一个利用 URL 语法，在命令行终端下使用的网络请求工具，支持 HTTP、HTTPS、FTP 等协议。CURL 也有用于程序开发使用的版本 libcurl。

Linux、MAC 一般系统默认已安装好 curl，直接在终端使用命令即可，如果需要手动安装，可以到 curl.haxx.se 下载安装。

Windows 系统 curl 下载地址: https://curl.haxx.se/windows/，下载解压后即可使用，命令的可执行文件在解压后的 bin 文件夹中。

2\. CURL命令用法
------------

#### 2.1 基础语法

```cobol
curl [options...] <url>
```

#### 2.2 常用参数

PS: (H) 表示只给 HTTP/HTTPS请求使用，(F) 表示只给 FTP请求使用。

(1)Show Info

```bash
-h/--help                       # 打印帮助信息-V/--version                    # 显示版本信息-s/--silent                     # 静默模式, 不输出任何内容-i/--include                    # 输出包含 headers 信息-v/--verbose                    # 输出详细内容-#/--progress-bar               # 以进度条方式显示传输过程
```

(2) Headers

```bash
-H/--header     LINE        (H) # 添加请求头, 可添加多个 -H 参数,                                 # 参数格式: -H "NAME: VALUE" -A/--user-agen  STRING      (H) # 请求头的 User-Agent 字段-e/--referer    URL         (H) # 请求头的 Referer 字段-r/--range      RANGE       (H) # 请求头的 Range 字段-b/--cookie     STRING/FILE (H) # 请求头的 Cookie 字段, 以字符串的形式提供,                                 # 或从指定 cookie 文件中读取 -c/--cookie-jar     FILE    (H) # 把响应头中的 cookie 保存到指定文件 -D/--dump-header    FILE        # 把 headers 信息保存指定文件-I/--head                       # 只显示文档信息（只显示响应头）
```

(3) Request Content

```cobol
# 执行命令, 如果是 HTTP 则是请求方法, 如: GET, POST, PUT, DELETE 等#          如果是 FTP 则是执行 FTP协议命令-X/--request    COMMAND # HTTP POST 请求内容（并自动发出 POST 请求）, 例如: aa=bb&cc=dd-d/--data       DATA    (H) # HTTP multipart POST 表单数据,（并自动发出 POST 请求）# 多个表单字段可添加多个 -H 参数, 如果是文件参数, 路径值前面需要加@# 参考格式: -F "name1=@/filepath" -F "name2=stringvalue"-F/--form       CONTENT (H)                   
```

(4) Response Content

```bash
-o/--output FILE    FILE        # 把响应内容输出到指定文件-O/--remote-name                # 以 URL 的文件名作为文件名称保存响应内容到当前目录-C/--continue-at    OFFSET      # 断点续传, 从 offset 位置继续传输
```

(5) Other

```cobol
-y/--speed-time     SECONDS     # 连接 超时时间, 单位: 秒,  默认为 30-m/--max-time       SECONDS     # 读取 超时时间, 必须在该时间内传输完数据, 单位: 秒--limit-rate        RATE        # 限速传输, 单位: Byte/s -x/--proxy          [PROTOCOL://]HOST[:PORT]    # 设置代理-U/--proxy-user     USER[:PASSWORD]             # 代理的用户名和密码-u/--user           USER[:PASSWORD][;OPTIONS]   # 设置服务器的用户密码和登录选项--cacert            FILE                  (SSL) # 使用指定的 CA 证书 -P/--ftp-port       ADR (F) # 指定 FTP 传输的端口-T/--upload-file    FILE    # 上传文件到指定的 URL (http/ftp) 位置,                             # 参考格式: -T "file1" 或 -T "{file1,file2}" -Q/--quote    CMD  (F/SFTP) # 执行命令, -X 只执行一条命令, -Q 可执行多条,                            # 多条命令将按顺序执行,                            # 参考格式: -Q "cmd1" -Q "cmd2"
```

3\. curl 命令使用实例
---------------

#### 3.1 HTTP/HTTPS 网络请求

(1)GET 请求

```cobol
curl https://www.baidu.com/         # GET请求, 输出 响应内容curl -I https://www.baidu.com/      # GET请求, 只输出 响应头curl -i https://www.baidu.com/      # GET请求, 输出 响应头、响应内容curl -v https://www.baidu.com/      # GET请求, 输出 通讯过程、头部信息、响应内容等  下载文件# 指定保存的文件名称下载文件curl https://www.baidu.com -o baidu.txt # 使用 URL 指定的资源文件名保存下载文件（URL 必须指向具体的文件名）curl https://www.baidu.com/index.html -O # 指定 Usaer-Agent 和 Referer 请求头的值, 下载文件curl -A "Mozilla/5.0 Chrome/70.0.3538.110 Safari/537.36" \     -e "https://www.baidu.com/" \     https://www.baidu.com/index.html -O  # 指定Authorization请求头的值, 下载文件# 参数格式: -H "NAME: VALUE"curl -H "Authorization: a112121dada" \     https://www.baidu.com/index.html -O
```

(2)POST 请求提交数据

```cobol
# POST 提交 JSON 数据（\表示命令语句还未结束, 换行继续）curl -H "Content-Type: application/json"                \     -d '{"username":"hello", "password":"123456"}'     \     http://localhost/login # POST 提交 表单数据curl -F "username=hello"                \     -F "password=123456"               \     -F "head_image=@filepath.jpg"      \     http://localhost/register
```

#### 3.2 FTP 上传/下载文件

假设 FTP 服务器 地址为:192.168.0.100; 用户名为:user; 密码为:passwd

(1)查看文件

```cobol
# 查看 FTP 指定目录（目录必须以"/"结尾）下的文件列表 curl ftp://192.168.0.100/aaDir/ -u "user:passwd" # 查看 FTP 指定文件的内容（直接输出到终端） curl ftp://192.168.0.100/aaDir/aa.txt -u "user:passwd" # 用户名 和 密码 的另一种写法（查看 FTP 服务器指定目录）curl ftp://user:passwd@192.168.0.200/aaDir/
```

(2)上传文件

```cobol
# 上传 aa.txt 文件到 FTP 指定目录下（目录必须以"/"结尾）, 并以 原文件名 命名保存curl ftp://192.168.0.100/aaDir/ -u "user:passwd" -T "aa.txt" # 上传 aa.txt 文件到 FTP 指定目录下, 并以 bb.txt 命名保存curl ftp://192.168.0.100/aaDir/bb.txt -u "user:passwd" -T "aa.txt" # 同时上传多个文件curl ftp://192.168.0.100/aaDir/ -u "user:passwd" -T "{aa.txt,bb.txt}"
```

 (3)下载文件

```cobol
# 下载 FTP 指定文件 /aaDir/aa.txt, 以原文件名命名保存到当前目录 curl ftp://192.168.0.100/aaDir/aa.txt -u "user:passwd" -O # 下载 FTP 指定文件 /aaDir/aa.txt, 以 bb.txt 命名保存curl ftp://192.168.0.100/aaDir/aa.txt -u "user:passwd" -o bb.txt
```

(4)执行 FTP 协议命令

```cobol
curl 执行 FTP 命令格式: 单条命令: curl [-options] <ftpUrl> -X "FTP命令"多条命令: curl [-options] <ftpUrl> -Q "FTP命令" -Q "FTP命令"# # 创建文件夹, 在 /aaDir/ 目录（目录必须以"/"结尾）下创建 bbDir 文件夹#curl -u "user:passwd" ftp://192.168.0.100/aaDir/ -X "MKD bbDir" # # 删除文件夹, 删除 /aaDir/ 目录下的 bbDir 文件夹（文件夹必须为空）#curl -u "user:passwd" ftp://192.168.0.100/aaDir/ -X "RMD bbDir" # # 删除文件, 删除 /aaDir/ 目录下的 aa.txt 文件#curl -u "user:passwd" ftp://192.168.0.100/aaDir/ -X "DELE aa.txt" ## 重命名, 重命名需要连续执行两条命令, 使用两个 -Q 参数连续执行两条命令（必须先 RNFR, 后 RNTO）#curl -u "user:passwd" ftp://192.168.0.100/ -Q "RNFR OldPath" -Q "RNTO NewPath"
```

*   CURL 文档主页: [https://curl.haxx.se/docs/manpage.html](https://curl.haxx.se/docs/manpage.html)
*   CURL 帮助指南: [https://curl.haxx.se/docs/manual.html](https://curl.haxx.se/docs/manual.html)

本文转自 <https://blog.csdn.net/u013514928/article/details/102810250>，如有侵权，请联系删除。