---
title: 在linux搭建jupyter服务
---

### 前言

jupyter notebook(lab)要通过浏览器使用，可见它本身就是一种网络服务。因此一个自然的想法就是把 jupyter 作为一个 linux 服务启用并且开放在局域网中。

#### 优点

1. 不用每次都启用并占用一个终端
2. 局域网内可以用浏览器使用 notebook，免去了繁琐的 ssh 登录过程

---

### 操作步骤

1. 安装 jupyter

```bash
pip install notebook
```

2. 启动 jupyter notebook

```bash
jupyter notebook --generate-config
```

3. 修改 jupyter 配置文件

```bash
vim ~/.jupyter/jupyter_notebook_config.py
```

在文件中找到以下内容并修改

```bash
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.port = 8888
c.NotebookApp.open_browser = False
```

其中，`c.NotebookApp.password` 需要使用以下命令生成

```bash
jupyter notebook password
```

4. 新增 jupyter 服务

```bash
vim /etc/systemd/system/jupyter.service
```

在文件中写入以下内容

```bash
[Unit]
Description=Jupyter Notebook
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/bin/python3 /usr/local/bin/jupyter notebook --config=/root/.jupyter/jupyter_notebook_config.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

5. 启动 jupyter 服务

```bash
systemctl start jupyter
```

6. 设置开机自启

```bash
systemctl enable jupyter
```

### 结语
至此，jupyter 服务已经搭建完成，可以在浏览器中输入 `http://ip:8888` 访问 jupyter notebook 了。同时，每次重启服务器后，jupyter 服务也会自动启动。
