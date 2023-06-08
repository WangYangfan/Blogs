🔔 **在服务器配置jupyter notebook并远程连接**

(Windows系统 `Win+;` 插入emoji)

***

**服务器端(Ubuntu)**

1. 安装jupyter notebook `pip install  jupyter notebook`
2. 生成配置文件 输入 `jupyter notebook --generate-config` 会自动生成[~/.jupyter/jupyter_notebook_config.py]
3. 生成密码 输入 `ipython` 进入编译器，然后在编译器依次输入 `from  notebook.auth import passwd` 和 `passwd()` 输入自定义密码并重新输入一次验证(如'123456'), 生成的密码('argon2:xxx')会保存到[~/.jupyter/jupyter_notebook_config.json]中，最后输入 `exit()` 退出编译器
4. 修改配置文件 `vim ~/.jupyter/jupyter_notebook_config.py` 在文件末尾添加(按[ESC]输入`:$`可以进入文件末尾)
```
c.NotebookApp.ip = '*'  # 允许访问此服务器的ip 星号表示任意ip
c.NotebookApp.password = 'argon2:xxx'   # 生成的密码
c.NotebookApp.open_browser = False  # 运行时不打开本地浏览器
c.NotebookApp.port = host_port  # 使用的端口一般是8888
c.NotebookApp.enable_mathjax = True # 启用MathJax
c.NotebookApp.allow_remote_access = True    # 允许远程访问
c.NotebookApp.allow_root = True           
```

**客户端(Windows)**

1. 连接到远程服务器 `ssh -L post:localhost:host_port username@server_ip -p ssh_port` 然后输入远程服务器的登录密码, 接下来为避免每次ssh输入复杂命令, 配置公私钥实现免密登录, 配置ssh config实现别名简写
2. 生成公私钥 如在github已经生成过公私钥则略过此步, 否则, `ssh-keygen -t rsa -C "email@xxx.com"` 会在[C:\Users\user\.ssh]生成私钥[id_rsa]和公钥[id_rsa.pub], 将[id_rsa.pub]的内容复制到服务器端[~/.ssh/authorized_keys], 若该文件不存在则需要创建
3. 配置ssh 在[C:\Users\user\.ssh]下创建[config]写入
```
Host server
HostName server_ip
LocalForward post localhost:host_port
User username
Port ssh_port
```
其中, Host别名, HostName服务器ip, User用户名和Port连接到服务器的端口号, LocalForward指定本地主机端口host_port通过ssh转发到指定远程主机post(都写8888也可以)

Host, HostName, User, Port配置完成, 则仅需 `ssh -L post:localhost:host_port server`

LocalForward配置完成, 则仅需 `ssh server` 即可带本地转发连接到服务器

4. 运行程序 连接进入到远程服务器shell后运行 `jupyter notebook` 后不要关闭
5. 打开浏览器 输入网址`localhost:8888`即可远程使用jupyter notebook