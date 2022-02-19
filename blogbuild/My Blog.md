Hexo+GitHub/云服务器搭建个人网站

### 一、Hexo + Github

优势：免费方便，不花一分钱，不需要服务器不需要后台，博客可以轻松打包，转移

劣势：速度受限，不稳定

个人最开始使用的是Github，但后来购买了新域名，为了域名备案又购买了云服务器，干脆将博客部署到了云服务器上……

步骤：

1. 准备工作（较为简单，不再过多赘述）

   - 注册Github账号并验证邮箱

   - 安装node.js以及git for windows

     node.js去官网下载即可

     git下载速度可能会非常慢，这里给出了我的资源链接，若是需要其他版本的git，请自行寻找。

     ```
     https://pan.baidu.com/s/1s300wkM8oUJV0OqrRmFUlQ
     提取码：z5bk
     ```

2. 创建仓库（存储网站上的所有内容）

   在Github上新建一个名为`github用户名.github.io`的仓库（必须是你的用户名，其它名称无效），将来网站访问地址就是`https://github用户名.github.io`

   ![创建仓库](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/创建仓库.png)

   ![创建git仓库2](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/创建git仓库2.png)

   注意：仓库创建成功后10~30分钟才会生效

3. 绑定域名（没有域名的跳过即可）

   1. 购买新域名，万网/godaddy，实名认证，看个人需求决定是否备案，备案的话需要购买云服务器或者虚拟主机，最好在域名实名认证后3~5天再进行备案。

   2. 阿里云配置新域名：

      - 控制台—>域名解析—>添加A记录

        - 记录类型：A记录

        - 主机记录：@

        - 解析线路：默认

        - 记录值：IP地址，打开cmd，运行ping xxx.github.io即可得到

        - 注：连接超时解决办法：C:\Windows\System32\drivers\etc\hosts中添加192.30.255.112  github.com git 
          185.31.16.184 github.global.ssl.fastly.net  

          (需要完美控制权限)

        - TTL：默认十分钟

      - 控制台—>域名解析—>添加CNAME记录

        - 记录类型：CNAME
        - 主机记录：WWW
        - 解析线路：默认
        - 记录值：域名 xxxx.github.io
        - TTL：默认十分钟

   3. 在项目source目录下新建文件CNAME.txt，文件保存域名地址，然后删掉.txt

   4. xxx.github.io->setting->github pages->custorm domain->添加购买的域名

   注意：若是想解除绑定，需要删除域名解析，删除CNAME.txt文件，删除github上绑定的域名，重新部署。此外，**还需清除浏览器缓存**，上述缺一不可。

4. 配置SSH key（若无特殊指明，以下命令均在 Git Bash中执行）

   ssh key用来连接本地和服务器

   ```
   $ cd ~/. ssh #检查本机已存在的ssh密钥
   ```

   如果提示：No such file or directory 说明你是第一次使用git，输入以下命令：

   ```
   ssh-keygen -t rsa -C "git邮箱地址"
   ```

   连续三次回车，最终会在在用户目录下的`.ssh`文件夹中生成一个`id_rsa.pub`文件，记事本打开并复制，打开github主页，进入个人设置 -> SSH and GPG keys -> New SSH key，将复制的内容粘贴到key，title随意发挥，保存。

5. 在本地测试SSH是否配置成功

   输入以下命令：

   ```
   $ ssh -T git@github.com
   ```

   若提示`You've successfully authenticated, but GitHub does not provide shell access`则表示连接成功。

   此时还需配置以下内容：

   ```
   $ git config --global user.name "liuxianan"// github用户名
   $ git config --global user.email  "xxx@qq.com"// github注册邮箱
   ```

6. Hexo

   Hexo是一个博客发布工具，支持Markdown格式，有很多优秀的插件以及主题。

   1. 本地新建目录，作为网站内容的根目录，cd进入该目录，安装hexo

      ```
      $ cd xxxx
      $ npm install -g hexo
      ```

   2. 初始化Hexo

      ```
   $ hexo init  # 有可能会出现错误，这时多尝试几次，实在不行，直接把https://github.com/hexojs/hexo-starter/中的内容下载到目录中
      ```
      
      执行完上述命令后，会在该目录下生成相关文件……
      
   3. 执行以下命令，在本地预览

      ```
      hexo g //生成
      hexo s //本地预览，会生成预览地址
      ```

      浏览器输入本地预览地址查看，此时hexo已经自行创建了一篇名为hello world的博文。

   4. 修改主题

      根据个人喜好来吧，我最开始使用的yilla主题，后面换成了Butterfly主题。

      ```
      cd blog根目录/
      git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/Butterfly
      ```

      修改根目录下`_config.yml`中的`theme: xxx`为`theme: butterfly`（注意缩进问题）

      如果没有pug以及stylus的渲染器，下载安装：
      
      ```
      npm install hexo-renderer-pug hexo-renderer-stylus --save
      ```
      
      然后重新执行以下命令：
      
      ```
      hexo clean # 清除，遇事不决直接hexo clean
      hexo g
      hexo s
      ```

7. 上传到Github

   还有关键的一步就是配置_config.yml中的deploy部分和URL部分：

   ```
   deploy:
    type: git
    repository: git@github.com:github用户名/GitHub用户名.github.io.git 
    branch: master
    message:
   
   # URL
   url: https://github用户名.github.io/
   root:
   ```

   运行以下命令推送到github：

   ```
   hexo deploy  or
   hexo d           //简写
   ```

   若是提示以下错误：

   ```
   Deployer not found: github 或者 Deployer not found: git
   ```

   则需要再安装一个插件：

   ```
   npm install hexo-deployer-git --save
   ```

   重新推送即可。

至此，hexo+github配置完毕。

### 二、Hexo + 云服务器

优势：相较于GitHub，访问速度更快，更稳定

劣势：需要购买云服务器(首单很便宜，恢复原价之后那叫一个贵)，配置繁琐，服务器到期之后若是想租用其他服务器，需要重新配置

步骤：

1. 购买大陆境内云服务器(阿里云、腾讯云……，个人购买的是阿里云esc.n4.small，1CPU，2GiB，1Mbps)

2. 配置云服务器（均在控制台中进行操作）：

   - 设置实例账户密码，linux服务器默认账户是root，密码自行设置

   - 设置VNC远程登录密码

   - 在控制台的安全组中找到配置规则，在配置规则选择手动添加80端口，`目的`处填写80，`源`对应的位置选择0.0.0.0/0

     注：后面需要使用的nginx Web服务器的默认端口为80，所以需要在服务器上开启80端口，确保可以通过云服务器IP地址访问nginx上的内容

3. 服务器搭建nginx环境，nginx是一款轻量级的 Web 服务器 / 反向代理服务器及电子邮件代理服务器，国内使用 nginx 网站用户有：百度、京东、新浪等等

   1. 安装nginx

      ***

      **血的教训**：本人最开始使用 yum install -y nginx 命令安装的nginx（图个简单省事），确实安装成功了，但是通过访问服务器公网IP地址后发现：

      ![4QNP@C4POIV`}0_BOGSXTRR](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/4QNP@C4POIV`}0_BOGSXTRR.png)

       但当时也没多想，毕竟nginx都安装成功了，nginx -v和nginx -t命令也都没有问题，于是直接进行了后面的步骤……。结果最终部署到云服务器上后出现403错误，按照[Nginx出现403 forbidden解决办法](https://blog.csdn.net/qq_35843543/article/details/81561240)一一进行排查，但均以失败告终，无奈只能初始化云盘，从头再来。再次执行上述命令后还是出现了同样的问题，最终决定按照如下方法进行nginx的安装！

      ***

      1. 远程连接服务器并登录root账户

      2. 安装PCRE(nginx使用PCRE解析正则表达式)

         ```
      yum install -y pcre pcre-devel
         ```

      3. 安装zlib库(nginx使用zlib对http的包进行解压)

         ```
         yum install -y zlib zlib-devel
         ```

      4. 安装GCC以及Open SSL

         ```
         yum install gcc-c++
         yum install -y openssl openssl-devel
         ```

      5. 新建nginx目录，用于下载以及解压nginx（此步不是必须，只要为了文件的存储更加直观明了）

         ```
      mkdir nginx
         ```

      6. 进入nginx目录(cd nginx)，下载nginx，[官网](http://nginx.org/en/download.html)的版本分为三种，开发版，最新稳定版，遗留的旧版本，**建议下载最新稳定版(Stable version)**

         ```
         wget -c https://nginx.org/download/nginx-xxxxx.tar.gz # xxxx对应版本号
         ```

      7. 解压并进入解压目录

         ```
         tar -zxvf nginx-xxxxx.tar.gz
         cd nginx-xxxxx
         ```

      8. 当前目录下检查nginx的默认配置（234步成功完成后，此步肯定也没有问题，若出现问题，检查一下上述库是否安装成功）

         ```
          ./configure
         ```

   2. 编译安装

      1. 在解压目录下执行以下命令：

         ```
         make
         make install
         ```

         注：此处可能会出现以下error信息：

         **error_one：**

         ```
         src/core/ngx_murmurhash.c:37:11: error: this statement may fall through [-Werror=implicit-fallthrough=]
                  h ^= data[2] << 16;
               ~~^~~~~~~~~~~~~~~~
         src/core/ngx_murmurhash.c:38:5: note: here
           case 2:
              ^~~~
         src/core/ngx_murmurhash.c:39:11: error: this statement may fall through [-Werror=implicit-fallthrough=]
               h ^= data[1] << 8;
                  ~~^~~~~~~~~~~~~~~
         src/core/ngx_murmurhash.c:40:5: note: here
         ```

         解决办法：

         ```
         cd 解压目录/objs/
         vim Makefile
         ```

         将其中的-Werror去掉，保存退出。

         ```
         CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g
         ```

            **error_two:**

         ![nginx_error_two](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/nginx_error_two.png)

         解决办法：vim打开src/os/unix/ngx_user.c，将对应的内容注释掉，简单粗暴。然后再重新回到解压目录，执行编译安装。

         **若是上述办法不能解决，建议更换nginx版本。**

      2. 查找安装路径

         ```
          whereis nginx
         ```

         一般为/usr/local/nginx

      3. 进入到/usr/bin/目录下设置快捷方式

         ```
         ln -s /usr/local/nginx/sbin/nginx nginx
         ```

      4. 回到根目录，运行nginx

         ```
         nginx
         ```

      5. 在电脑上访问服务器的公网ip地址，看到如下界面即为安装成功！

         ![nginx配置成功](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/nginx配置成功.png)

      6. 在服务器上通过如下命令可以查看nginx**配置文件路径**以及对应的文件语法是否正确

         ```
         nginx -t
         ```

         ![nginx配置文件路径](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/nginx配置文件路径.png)

         查看版本

         ```
         nginx -v
         ```

   3. 配置nginx

      1. 关闭nginx

         ```
         # 快速关闭
         nginx -s stop
         # 有序而完整地关闭
         nginx -s quit
         ```

      2. 进入配置文件目录，打开nginx.conf

         ```
         cd usr/local/nginx/conf    # 安装目录
         vi nginx.conf
         ```

      3. 将第一行的 user 改为 root，并取消注释

         ![nginx配置修改1](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/nginx配置修改1.png)

      4. 在http的server中更改root路径，此路径为网站根目录（尽量不要存储在var等关键目录里，本人设置的为/home/xxxblog）。注意server可能有多个，分别对应80,443等端口号，默认为80端口(也就是通过http访问)，在80端口对应的server修改路径即可。

      5. `server_name`处填写公网ip地址

         ![nginx配置修改2](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/nginx配置修改2.png)

         注意：此处只是针对域名访问以及80端口进行的设置，下面会有针对域名访问，禁用ip访问，443端口(https)配置，ssL证书安装的的详细说明！建议先按照教程配置，以确保可以通过`http://IP地址/`访问到服务器上的内容！

      6. 创建对应的网站根目录

         ```
         cd ~/home
         mkdir xxxblog   # 确保和root中的地址相同
         ```

4. 安装Node.js和git

   1. 安装Node.js

      Node.js的安装方法有很多，此处给出本人使用的方法：

      1. cd 进入xxx目录，下载

         ```
         wget https://nodejs.org/dist/v14.8.0/node-v14.8.0-linux-x64.tar.xz # 14.8.0版本，其他版本可去官网寻找
         ```

      2. 解压

         ```
         tar -xf  node-v14.8.0-linux-x64.tar.xz
         ```

      3. vim /etc/profile查看环境变量，在末尾添加以下命令

         ```
         export NODE_HOME=/root/node/node-v14.8.0-linux-x64 #这里填解压之后node的路径
         export PATH=$PATH:$NODE_HOME/bin
         ```

      4. 刷新权限

         ```
         source /etc/profile
         ```

      5. 检测是否安装成功，`cd /`回到主目录，运行以下命令

         ```
         node -v
         npm -v
         ```

         出现对应版本号即可，若是没有找到该命令，可能是环境变量里的路径有误，注意是绝对路径。

   2. 安装git以及配置仓库

      1. 根目录下安装git

         ```
         yum install git
         ```

         git 安装完应该在`/home`目录下

      2. 配置git用户

         ```
         adduser git
         ```

      3. 修改用户权限

         ```
         chmod 740 /etc/sudoers
         ```

      4. 打开sudoers文件

         ```
         vi /etc/sudoers
         ```

         ![git配置](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/git配置.png)

      5. 在上图位置添加命令（注意缩进，保持一致即可）

         ```
         git		ALL=(ALL)      ALL
         ```

      6. 修改sudoers权限

         ```
         chmod 400 /etc/sudoers
         ```

      7. 设置git用户的密码

         ```
         sudo passwd git
         ```

      8. 切换至git用户，创建 ~/.ssh 文件夹和 ~/.ssh/authorized_keys 文件，将本地生成的id_rsa.pub文件中的公钥复制到authorized_keys中

         ```
         su git
         mkdir ~/.ssh
         vim ~/.ssh/authorized_keys
         ```

      9. 输入以下命令，赋予权限

         ```
         chmod 600 /home/git/.ssh/authorized_keys
         chmod 700 /home/git/.ssh
         ```

      10. 本地git中测试是否能免密登录git，桌面右键，点击git bash here

            ```
            ssh -v git@云服务器ip
            ```

            可能会出现以下错误：

            ![git本地验证出错](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/git本地验证出错.png)

            可以看到错误提示行：

            ```
            Offending ECDSA key in /c/Users/jonty/.ssh/known_hosts:3
            ```

            解决：打开本地的/c/Users/jonty/.ssh/known_hosts文件，将第3行删除即可。

            重新执行命令，提示以下信息时表示连接成功：

            ![git本地配置成功](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/git本地配置成功.png)

      11. 返回服务器，切换到root账户，创建git仓库目录，并赋予权限

             ```
             su root
             mkdir /home/repo # 目录自己定义
             chown -R git:git /home/repo
             chmod -R 755 /home/repo
             ```

      12. 注意还要给网站根目录(也就是配置nginx时设置的目录)赋予权限

             ```
             chown -R git:git /home/gxyblog  # 网站根目录
             chmod -R 755 /home/gxyblog
             ```

      13. 在git仓库目录创建一个空的git仓库

             ```
             cd /home/repo
             git init -bare hexo.git
             ```

      14. 在git仓库目录/hexo.git下的 hooks 文件夹中新建文件 post-update

             ```
             vi /home/repo/hexo.git/hooks/post-update
             ```

      15. 输入以下内容

             ```
             git --work-tree=/home/gxyblog --git-dir=/home/repo/hexo.git checkout -f  # 第一个目录为网站根目录  第二个目录为git仓库目录
             ```

      16. 修改post-update权限

             ```
             hown -R git:git /home/repo/hexo.git/hooks/post-update
             chmod +x /home/repo/hexo.git/hooks/post-update
             chown -R git:git /home/repo/hexo.git
             ```

5. 本地推送部署

   在本地blog根目录下，找到站点配置文件_config.yml，找到deply和url的配置，进行修改：

   ```
   deploy:
    type: git
    repository: git@云服务器的公网IP:/home/repo/hexo.git  # 目录为git仓库的地址
    branch: master
    message:
    
    
   # URL
   url: http://公网ip地址/
   root: 
   ```

6. 通过http://公网ip地址/进行访问，注意，此时https://公网ip地址/不可访问，因为nginx中使用的为80端口，若想使用https可按照如下教程进一步配置。

至此，部署在github和服务器上的步骤已经完成，可以通过切换deploy中的repo和URL中的网址来部署在GitHub或者云服务器上，个人网站既可以通过https://xxxx.github.io访问，也可以通过http://公网ip地址/访问(前提是都已经部署)。等后期可以购买备案域名，在域名解析中直接指向云服务器或者github对应的ip地址即可。

***

三、配置域名访问(同时禁止IP访问)，443端口，ssl证书

最近备案的域名下来了，在这里记录一下个人配置域名访问以及开放443端口的相关信息吧

1. 配置域名访问

   首先说明，如果想通过域名访问ngixn上的内容，需要满足以下条件：

   1. 域名解析到服务器

   2. 服务器绑定域名，阿里云linux服务器绑定域名官方文档戳[这里](https://help.aliyun.com/knowledge_detail/41091.html)。

   只绑定域名或者域名解析到服务器或者做跳转解析都是无效的！

   但是我再配置过程中发现了一个严重的问题：

   当域名解析到服务器ip地址后就可以直接跳转了，而nginx.conf中的`server_name`并没有填写对应的域名。。。what the f**k? 那岂不是任何人的域名都可以访问到我ngixn Web服务器上的内容吗。后来咨询了阿里云工程师，再结合我搜集的资料，发现是nginx.conf中没有禁止空主机头的原因。如果 Nginx 没有禁止空主机头，那么任意域名解析指向到服务器IP，都可以访问站点！此外，通过IP访问服务器上内容也是存在一定风险的，所以尽量设置禁止通过IP访问。

   下面说下具体的配置步骤：

   1. 禁止空主机头以及禁止IP访问！！！

      1. 连接服务器并登录，停止nginx服务

         ```
         nginx -s quit
         ```

      2. 打开nginx配置文件(nginx -t可以查看文件位置)

         ```
         vim /usr/local/nginx/conf/nginx.conf
         ```

      3. 在80端口对应的sever前加上如下命令：

         ```
         server{
         	listen  80 default_server;
         	server_name _;   # 禁止IP访问
         	return 500;
         	}
         ```

         如下图h黄色区域所示：

         ![80端口配置](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/80端口配置.png)

      4. 测试

         此时的`server_name`可以先保留为服务器ip地址，先测试一下结果是否符合我们的预期。

         直接运行`nginx`命令，开启nginx服务。确保

         1. 域名解析到服务器ip地址
         2. server_name为ip，也就是说还未绑定域名

         此时的结果应该是：既不能通过ip访问，也不能通过域名进行访问。如果结果不符合预期，请检查空主机头是否配置正确或者清除浏览器缓存重新加载！

   2. 域名解析

      略

   3. 服务器绑定域名

      1. 停止ngixn服务

      2. 打开nginx.conf配置文件，在`server_name`处添加域名，和解析的域名保持相同即可。如果解析的是` xxxx.com`，那就写`xxxx.com`，不要写成`www.xxxx.com`!
      3. 重新启动ngixn服务

   4. 最终测试

      1. 在浏览器中可以通过域名访问！

      2. 在本地git中可以连接到域名

         ```
         ssh -v git@域名
         ```

   5. 修改本地hexo根目录站点配置文件

      deploy部分既可以设置为

      ```
      repository: git@服务器ip:/home/repo/hexo.git
      ```

      也可以设置为

      ```
      repository: git@域名:/home/repo/hexo.git
      ```

      url部分由原来的`http://ip/`修改为

      ```
      http://域名/
      ```

      

2. 开启https

   当我们在浏览器中输入绑定的域名`xxxx.com`进行访问时，网址默认格式为`http://xxxx.com`，同时浏览器会提示网址不安全！而https是一种通过计算机网络进行安全通信的传输协议，经由http进行通信，利用SSL/TLS建立全信道，加密数据包。HTTPS使用的主要目的是提供对网站服务器的身份认证，同时保护交换数据的隐私与完整性。所以开启https是非常有必要的！

   1. 添加443端口

      在服务器实例—安全组—配置规则—中添加443端口

      ![443开放](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/443开放.png)

   2. 购买下载SSL证书

      阿里云每年可以免费申请20个证书，购买之后，点击申请证书，按照要求填写相关信息，等待生成即可

      ![证书配置](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/证书配置.png)

      生成之后点击下载，选择nginx版本。

   3. nginx.conf配置

      登录服务器，执行以下命令

      ```
      nginx -s quit # 停止服务
      cd /usr/local/nginx
      mkdir cert
      ```

      通过远程连接工具将下载到本地的ssl证书传送到cert目录下

      打开nginx.conf文件，在443端口处配置以下内容：

      ```
      server {
          listen 443 ssl; # 如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on
          server_name 绑定的域名; 
          ssl_certificate cert/cert-file-name.pem;  # 需要将cert-file-name.pem替换成已上传的证书文件的名称
          ssl_certificate_key cert/cert-file-name.key; # 需要将cert-file-name.key替换成已上传的证书私钥文件的名称
          ssl_session_timeout 5m;
          ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
          ssl_prefer_server_ciphers on;
          location / {
              root html;  #站点目录。
              index index.html index.htm;
          }
      ```

      同时还要添加禁止空主机头，在上述代码前加上如下代码：

      ```
      server {
      listen 443 ssl;
      server_name _;
      ssl_certificate 随便设置一个ssl证书;
      ssl_certificate_key 随便设置一个ssl证书的key;
      return 500;
      }
      ```

      设置`http`请求自动跳转`https`，如果希望所有的`http`访问自动跳转到`https`页面，则可以在需要跳转的`http`站点下添加以下`rewrite`语句。（紫色方框区域）

      ![80端口配置](https://gxyblogpic.oss-cn-beijing.aliyuncs.com/img/80端口配置.png)

      ```
       rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
      ```

      注意，如果把80端口注释掉，只保留443端口的话，在访问网站时，只能输入`https://xxxx.com/`进行访问，无法直接输入`xxxx.com`访问，因为会自动跳转到`http://xxxx.com`，而该端口是被禁止的！

   4. 重新启动nginx

      ```
      cd /usr/local/nginx/sbin  #进入Nginx服务的可执行目录
      ./nginx -s reload  #重新载入配置文件
      ```

      可能会出现的错误以及解决办法：

      1. ```
         nginx: [emerg] the "ssl" parameter requires ngx_http_ssl_module in /...
         ```

         缺少ngx_http_ssl_module模块。解决办法：戳[这里](https://www.cnblogs.com/ghjbk/p/6744131.html)，亲测有效

      2. ```
         nginx: [emerg] cannot load certificate “/ssl/w“error:2006D080:BIO routines:BIO_new_file:no such file
         ```

         ssl文件相对路径错误。解决办法：查看`ssl_certificate`和`ssl_certificate_key`对应的ssl文件路径是否正确！

      3. ```
         nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
         ```

         端口被占用，解决办法：

         ```
         # 方法一
         fuser -k 80/tcp  # 若是443端口出现问题，命令中的80换成443即可
         
         # 方法二
         ps -A | grep nginx   # 查看占用的进程号
         kill -9 pid1    # 杀死进程
         kill -9 pid2    # 杀死进程
         ```

   5. 配置站点文件中的url

      ```
      https://域名/
      ```

   至此，已经全部配置完毕！在浏览器中输入域名，会自动跳转到`https://xxxx.com`，并且浏览器中不会出现安全提示信息！

   

   

My Blog （在不同电脑上转移）

1. [安装git](https://blog.csdn.net/m0_37450089/article/details/120939158)，node.js
2. 在git中配置SSH key：解决本地和服务器连接的问题
   1. $ cd ~/. ssh #检查本机已存在的ssh密钥
   2. ssh-keygen -t rsa -C "邮件地址"
   3. 连续三次回车，找到C:/user/.ssh/id_ras.pub文件，复制内容到GitHub主页-个人设置-SSH and GPG keys - New SSH key（内容粘贴到key中，title随便填写）
   4. 测试是否成功：ssh -T  git@github.com
   5. 成功后需要配置：
      - git config --global user.name "xxxx" // github用户名
      - git config --global user.email  "xxx@qq.com" // github注册邮箱
3. 在git中通过 npm install -g hexo命令安装hexo
4. 在blog目录(转移的)里运行git bash
5. 命令
   - hexo c 清除
   - hexo g  生成
   - hexo s  本地预览
   - hexo deploy 推送远端

6. hexo+github创建个人博客

   























github转移到阿里云服务器

1. 购买云服务器（真贵啊，不过首单打0.26折，最便宜的是38一年，可以以同一价格续费三次，之后恢复原价。。。）
2. 云服务器控制台-设置实例登录密码-远程连接（VNC）-设置VNC登录密码-输入实例登录账号密码
3. 

