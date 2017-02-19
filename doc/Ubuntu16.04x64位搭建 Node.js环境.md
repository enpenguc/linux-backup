# Ubuntu 16.04 64位搭建NodeJS环境

## 下载nodejs

   推荐v6.9.5长期支持版node-v6.9.5-linux-x64.tar.xz(当前时间201701)，使用淘宝镜像官网是https://npm.taobao.org/mirrors/node下载。
## 安装nodejs
  1. 下载并解压 node-v6.9.5-linux-x64.tar.xz

      ```bash
      tar -xJf node-v6.9.5-linux-x64.tar.xz   
      ```


  2. 移动解压目录到软件安装目录 /opt/

      ```bash
      sudo mv node-v6.9.5-linux-x64 /opt/node    
      ```


  3. 安装 npm 和 node 命令到系统命令

      ```bash
      sudo ln -s /opt/node/bin/node /usr/local/bin/node
      sudo ln -s /opt/node/bin/npm /usr/local/bin/npm
      ```


   4. 验证：

      ```bash
      node -v
      # 显示输出
      v6.9.5

      npm -v
      # 显示输出
      3.10.10
      ```

      ​
## 设置 npm 使用淘宝源
1. 修改~/.bashrc ，添加alias

   注：如果使用的是zsh，则修改~/.zshrc

   ```bash
   # 1.先备份
   cp ~/.bashrc ~/.bashrc.bak
   # 2.修改~/.bashrc
   gedit ~/.bashrc
   # 3.在最后面添加以下配置
   alias cnpm="npm --registry=https://registry.npm.taobao.org \
   --cache=$HOME/.npm/.cache/cnpm \
   --disturl=https://npm.taobao.org/dist \
   --userconfig=$HOME/.cnpmrc"
   ```


2. 使修改立即生效

   ```bash
   source ~/.bashrc
   ```


3. 验证，使用淘宝镜像安装 npm 包

   ```bash
   cnpm -v
   # 显示输出
   3.10.10
   # 安装依赖，和使用npm一致
   cnpm install 软件包名
   ```
