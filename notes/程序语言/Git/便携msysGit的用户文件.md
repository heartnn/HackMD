# 便携msysGit的用户文件

使用 [msysGit](http://msysgit.googlecode.com/) 官方提供的便携版本使我们能在不安装软件的情况下使用 [Git](https://git-scm.com/) 的相关操作，但是有一个问题就是 msysGit 默认将用户的个人设置保存在当前 Windows 用户的用户目录中，所以要实现真正意义上的便携还需要将用户文件保存在其它位置（比如 USB 的某个目录中），同时下面所介绍的实现均为不改变已有文件的情况下进行，而是采用新文件调用已有文件的方式。

## 自定义用户文件的存储位置

因 msysGit 官方便携版中的 Git bash.bat 文件不支持参数调用，所以优先使用支持参数调用的 Git bash.vbs，但仍会给出使用 bat 文件实现自定义用户文件的存储位置。

### VBScript版

```vbscript
Set fso = CreateObject("Scripting.FileSystemObject")
Set shell = CreateObject("WScript.Shell")

Rem 获得 Git 目录。
gitdir = fso.GetParentFolderName(WScript.ScriptFullName)

Rem 设置当前进程的 HOME 环境变量。
homedir = gitdir
Set env = shell.environment("process")
env("HOME") = "/" + replace(replace(homedir, "\", "/"), ":", "")

Rem 调用 Git Bash.vbs 文件。
gitvbs = """" + gitdir + "\Git Bash.vbs"""
If WScript.Arguments.Length > 0 Then
    shell.run gitvbs + " """ + WScript.Arguments(0) + """"
Else
    shell.run gitvbs + " """ + gitdir + """"
End If
```

### BAT版

```dos
@rem 注：官方（1.7.9 版）git-cmd.bat 不支持路径中包含空格。
@echo off

rem 获得 Git 目录。
set gitdirbare=%~dp0
set gitdir="%gitdirbare%"

rem 设置当前进程的 HOME 环境变量。
set HOME=%gitdir%

rem 调用 Git Bash.bat 文件。
set gitbat="%gitdirbare%git-cmd.bat"
%gitbat% %1
```

## 右键菜单

尽管现在已经支持将工作目录做为参数运行 Git 环境，但是我们更愿意使用安装版提供的右键菜单方式更方便的进入工作目录。如果环境允许则建议使用这种方式，不过需要在替换为你所在环境的路径，当然为了方便，你可以编写 VBScript 脚本动态识别路径。

### 添加

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\Background\shell\Git Bash]
@="Icon"=hex(2):22,00,53,00,3a,00,5c,00,41,00,70,00,70,00,6c,00,69,00,63,00,61,\00,74,00,69,00,6f,00,6e,00,73,00,5c,00,6d,00,73,00,79,00,73,00,47,00,69,00,\74,00,5c,00,65,00,74,00,63,00,5c,00,67,00,69,00,74,00,2e,00,69,00,63,00,6f,\00,22,00,00,00
[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\Background\shell\Git Bash\command]
@="wscript.exe \"S:\\Applications\\msysGit\\git-bash-portable.vbs\" pushd \"%V\""

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\Git Bash]
@="Icon"=hex(2):22,00,53,00,3a,00,5c,00,41,00,70,00,70,00,6c,00,69,00,63,00,61,\00,74,00,69,00,6f,00,6e,00,73,00,5c,00,6d,00,73,00,79,00,73,00,47,00,69,00,\74,00,5c,00,65,00,74,00,63,00,5c,00,67,00,69,00,74,00,2e,00,69,00,63,00,6f,\00,22,00,00,00
[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\Git Bash\command]
@="wscript.exe \"S:\\Applications\\msysGit\\git-bash-portable.vbs\" \"%1\""
```

### 移除

```reg
Windows Registry Editor Version 5.00

[-HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\Background\shell\Git Bash]
[-HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\Git Bash]
```

---
via: http://baifa.me/2011/02/msysgit.html
