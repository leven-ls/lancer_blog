---
title: 通过chrome debug协议启动selenium
date: 2020-11-26 15:16:07
tags:
---

## 通过 pywinauto 来进行启动 chrome

通过这种方式启动的chrome，可以绕开一些反爬虫机制，比如大众点评的验证码界面
``` python
from pywinauto.application import Application
from selenium import webdriver

chrome_path = "C:/Program Files (x86)/Google/Chrome/Application/chrome.exe"
debug_port = 7979
# 初始化一个空的文件夹来存放新的chrome user data
user_data_dir = "D:/userdata"
open_command = "{0} --remote-debugging-port={1} --user-data-dir={2}".format(
    chrome_path, debug_port, user_data_dir
)
app = Application(backend="uia").start(open_command)

options = webdriver.ChromeOptions()
options.debugger_address = "127.0.0.1:{0}".format(debug_port)

driver = webdriver.Chrome(options=options)
driver.get("https://www.baidu.com")
```