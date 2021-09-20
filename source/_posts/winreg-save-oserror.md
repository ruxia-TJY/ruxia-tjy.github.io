---
title: winreg导出注册表出现OSError[WinError 1314] 客户端没有所需的特权
date: 2021-08-30 09:10:47
tags: 
  - Python
toc: true
categories: python
---

Winreg在导出注册表时候会出现OSError[WinError 1314] 客户端没有所需的特权错误。
<!--more-->
原因是导出注册表需要SeBackPrivileg安全权限（才知道特权跟权限不一样）

一开始在百度和bing上找不到答案，但是google搜索第一个就是！！！

答案在StackOverFlow上面。原链接：https://stackoverflow.com/questions/30984406/winreg-savekey-error-a-required-privilege-is-not-held-by-the-client

```python
import os, sys
import winreg
import win32api
import win32security

#
# You need to have SeBackupPrivilege enabled for this to work
#

priv_flags = win32security.TOKEN_ADJUST_PRIVILEGES | win32security.TOKEN_QUERY
hToken = win32security.OpenProcessToken (win32api.GetCurrentProcess (), priv_flags)
privilege_id = win32security.LookupPrivilegeValue (None, "SeBackupPrivilege")
win32security.AdjustTokenPrivileges (hToken, 0, [(privilege_id, win32security.SE_PRIVILEGE_ENABLED)])

key = winreg.OpenKey(winreg.HKEY_LOCAL_MACHINE, r'SOFTWARE\Bandizip')

filepath = 'key.reg'

if os.path.exists (filepath):
  os.unlink (filepath)
winreg.SaveKey(key, filepath)
```