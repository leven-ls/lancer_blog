---
title: jupyterlab折腾指南
date: 2020-12-10 11:26:27
tags:
---

### 创建一个py环境
基本每个独立的项目都建议建立一个自己虚拟环境，保证互相之前不污染。
```bash
[root@VM-0-16-centos jupyterlab_folder]# python3 -m venv env
[root@VM-0-16-centos jupyterlab_folder]# source env/bin/activate
# 试试是否成功
(env) [root@VM-0-16-centos jupyterlab_folder]# python
Python 3.6.8 (default, Nov 16 2020, 16:55:22) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-44)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 

# 更新一下Pip
(env) [root@VM-0-16-centos jupyterlab_folder]# pip install --upgrade pip

# pypi官方源速度不是很快，可以切换到清华源
# 另外吐槽下，阿里源跟腾讯源，都自己做过莫名其妙改动，可能会引发未知错误
# 比如安装 jupyterlab，腾讯源就可能会报404
(env) [root@VM-0-16-centos jupyterlab_folder]# pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple


# 安装jupyterlab
(env) [root@VM-0-16-centos jupyterlab_folder]# pip install jupyterlab


# 生成config文件
(env) [root@VM-0-16-centos jupyterlab_folder]# jupyter-lab --generate-config
Writing default config to: /root/.jupyter/jupyter_notebook_config.py


# 生成远程访问密码
(env) [root@VM-0-16-centos jupyterlab_folder]# python
Python 3.6.8 (default, Nov 16 2020, 16:55:22) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-44)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from notebook.auth import passwd
>>> passwd()
Enter password: 
Verify password: 
'argon2:$argon2id$v=19$m=10240,t=10,p=8$doarOadadSFSFSAFWJ+W2J1Ng$aJIePQ'
>>> 


# 修改config文件
c.NotebookApp.allow_remote_access = True
c.NotebookApp.open_browser = False
c.NotebookApp.ip = '*'
c.NotebookApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$doarOiO2G8'

# 指定一个放notebook的文件夹
c.NotebookApp.notebook_dir = '/root/jupyterlab_folder/'

# 因为在nginx指定了一个子路径，所以notebook config里面也跟着修改下
c.NotebookApp.base_url = '/jupyterlab/'

```

### 修改nginx对应配置
``` js
location /jupyterlab/ {
    proxy_pass http://localhost:8888;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

### 启动 jupyterlab
#### 以systemd方式启动
```bash
# 进入目录
/usr/lib/systemd/system

# 新建 jupyter.service
[UNIT]
#服务描述
Description=jupyterlab
#指定了在systemd在执行完那些target之后再启动该服务
After=network.target

[Service]
#定义Service的运行类型
Type=simple
PIDFile=/run/jupyter.pid
#定义systemctl start|stop|reload *.service 的执行方法（具体命令需要写绝对路径）
ExecStart=/root/pys/jupyterlab_folder/env/bin/jupyter lab --allow-root
WorkingDirectory=/root/pys/jupyterlab_folder
Restart=always
RestartSec=10

#创建私有的内存临时空间
PrivateTmp=True

[Install]
#多用户
WantedBy=multi-user.target
```

### 启动service
``` bash
# 先enable
sudo systemctl enable jupyter.service

# 重启
sudo systemctl daemon-reload
sudo systemctl restart jupyter.service
```

### 然后就输入之前的密码登录进行了
![()](/img/vscode环境/TIM截图20201210143221.png)
