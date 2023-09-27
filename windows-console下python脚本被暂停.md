---
title: windows console下python脚本被暂停
date: 2021-01-11 11:19:52
tags:
---

之前有给另外个部门写了个脚本帮助他们完成任务，后来他们告诉说程序会暂停，但是输入 enter 后程序又恢复运行了

然后做了个简单的测试

```
cnt = 0

while True:
    cnt += 1
    print(cnt)
    time.sleep(1)
```

发现在 windows console 下，如果选中了当前 console，然后整个程序就会暂停。
结果居然是 console 的 feature，解决方法
![()](/img/vscode环境/GGPXR.gif)
或者在程序里面

```
def quickedit(enabled=1):  # This is a patch to the system that sometimes hangs
    import ctypes

    """
        Enable or disable quick edit mode to prevent system hangs, sometimes when using remote desktop
        Param (Enabled)
        enabled = 1(default), enable quick edit mode in python console
        enabled = 0, disable quick edit mode in python console
        """
    # -10 is input handle => STD_INPUT_HANDLE (DWORD) -10 | https://docs.microsoft.com/en-us/windows/console/getstdhandle
    # default = (0x4|0x80|0x20|0x2|0x10|0x1|0x40|0x200)
    # 0x40 is quick edit, #0x20 is insert mode
    # 0x8 is disabled by default
    # https://docs.microsoft.com/en-us/windows/console/setconsolemode
    kernel32 = ctypes.windll.kernel32
    if enabled:
        kernel32.SetConsoleMode(
            kernel32.GetStdHandle(-10),
            (0x4 | 0x80 | 0x20 | 0x2 | 0x10 | 0x1 | 0x40 | 0x100),
        )
        print("Console Quick Edit Enabled")
    else:
        kernel32.SetConsoleMode(
            kernel32.GetStdHandle(-10),
            (0x4 | 0x80 | 0x20 | 0x2 | 0x10 | 0x1 | 0x00 | 0x100),
        )
        print("Console Quick Edit Disabled")


quickedit(0)  # Disable quick edit in terminal
```
