# Ubuntu Server 安装 shadowsocks 客户端

## 安装步骤

1. 安装 shadowsocks

   ```bash
   # 如果python-pip没安装，则安装。已经安装则忽略
   $ apt-get install python-pip
   # 安装shadowsocks
   $ pip install shadowsocks
   ```

2. 配置 shadowsocks

   ```bash
   # 配置文件shadowsocks.json文件
   $ sudo vi /etc/shadowsocks.json
   ```

   填写如下配置项

   ```json
   {
     "server": "您服务器地址",
     "server_port": 9999,
     "local_address": "127.0.0.1",
     "local_port": 1080,
     "password": "密码",
     "timeout": 600,
     "method": "aes-256-cfb"
   }
   ```

3. 启动 shadowsocks

   ```bash
   # 后台启动（此命令启动后，会启动一个sock5代理服务）
   $ sslocal -c /etc/shadowsocks.json -d start
   # 查看启动进程
   $ ps -ef | grep sslocal
   ```

   `注意`：启动 shawdowsocks 服务后，只是启动一个 sock5 代理服务，此时我们的 http/https 还是无法访问，此时就需要将 http 服务转换为 sock5。我们可以借助 `polipo` 来实现。

4. 安装配置 polipo

   ```bash
   # 安装polipo
   $ sudo apt-get install polipo
   # 配置polipo
   $ sudo vi /etc/polipo/config
   # 配置项目如下：
   logSyslog = true
   logFile = /var/log/polipo/polipo.log
   proxyAddress = "0.0.0.0"
   socksParentProxy = "127.0.0.1:1080"
   socksProxyType = socks5
   chunkHighMark = 50331648
   objectHighMark = 16384
   serverMaxSlots = 64
   serverSlots = 16
   serverSlots1 = 32
   # 配置成功后，重启polipo服务
   $ sudo /etc/init.d/polipo restart
   ```

5. 设置终端命令行代理

   > polipo 的默认端口号是 8123

   ```bash
   # 设置http_proxy、https_proxy代理
   $ export http_proxy=http://127.0.0.1:8123;export https_proxy=http://127.0.0.1:8123;
   # 注：如果要取消代理，则执行如下命令
   $ unset http_proxy
   $ unset https_proxy
   ```

6. 测试

   ```bash
   # ip.sb站点测试，如果返回ip为shadowsocks的服务地址，则说明成功
   $ curl ip.sb
   # ip.cn站点测试
   $ curl ip.cn
   # curl测试,返回html内容则表示成功
   $ curl www.google.com
   ```

7. 设置终端长期代理

   ```bash
   # 修改bash配置.bashrc
   $ sudo vi /.bashrc
   # 配置添加
   export http_proxy=http://127.0.0.1:8123;export https_proxy=http://127.0.0.1:8123;
   # 重启source
   $ source ~/.bashrc
   ```

8. 服务器重启后随即启动配置

   ```bash
   # 服务器重启后，手工重启shadowsocks
   $ sslocal -c /etc/shadowsocks.json -d start
   # 配置随机启动
   ```

9. 附录：git 代理设置

   9.1. 基本知识：git clone有两种形式，一个是走http(https),另一种是ssh。两种方式的代理设置不一样。两种方式示例如下：
   ```bash
   # 使用ssh方式(Clone with SSH)
   $ git clone git@github.com:enpenguc/linux-backup.git
   # 使用https方式(Clone with HTTPS)
   $ git clone https://github.com/enpenguc/linux-backup.git
   ```
   9.2. http(https)代理设置如下：

   ```bash
   # git设置https代理
   $ git config --global https.proxy https://127.0.0.1:8123
   # 或者走socks5 代理（如Shadowsocks客户端,MAC下sock5端口设置为1087）
   $ git config --global https.proxy "socks5://127.0.0.1:1087"
   # 设置了https代理后即可通过https方式clone代码
   $ git clone https://github.com/enpenguc/linux-backup.git
   # 另附：取消https代理命令，如下
   $ git config --global --unset https.proxy
   ```
   
   9.3. ssh方式：
   
   9.3.1 修改`~/.ssh/config` 文件（不存在则新建）
   
   ```bash
   # 修改~/.ssh/config文件
   $ vim ~/.ssh/config
   ```
   
   9.3.2 填入一下配置（注意`方式1`、`方式2`开启一个即可，如当前开启`方式2：走 socks5 代理（如 Shadowsocks）`）
   
   ```bash
   # 必须是 github.com
   Host github.com
      HostName github.com
      User git
      # 方式1：走 HTTP 代理
      # ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=1087
      # 方式2：走 socks5 代理（如 Shadowsocks，当前mac的socks配置端口为1086）
      ProxyCommand nc -v -x 127.0.0.1:1086 %h %p
   ```
   
   9.3.3 配置好ssh代理后，即可使用ssh方式clone代码
   
   ```bash
   # 使用ssh方式(Clone with SSH)
   $ git clone git@github.com:enpenguc/linux-backup.git
   ```
 
## FAQ

1. ping 测试不通, `ping www.google.com`没用响应

   > ping 测试不通，这是正常的。原因 sock 代理走 TCP/UDP 协议,而 ping 命令走 ICMP 协议。ping 一个网站地址，操作系统需要请求 DNS 服务器，解析到 IP 地址，才能够发出 ICMP 包。ping 不通 google.com 只能说明当前的网络 DNS 服务器不解释此域名。
   > 相关问题见
   > https://stackoverflow.com/questions/5274934/use-ping-through-socks-server
