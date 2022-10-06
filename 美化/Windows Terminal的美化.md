## 前置准备

### Winget

微软的包管理器，用于下载安装应用，可以去微软商店下载

### Windows Terminal

win11自带，win10可以去微软商店下载，Terminal可以集成多个命令面板，对命令面板进行美化

### oh my posh

利用oh my posh可以更加美化Terminal，里面有很多的主题

下载oh my osh

```java
winget install JanDeDobbeleer.OhMyPosh -s winget
```

oh my posh有很多图标，最好使用兼容范围更广的字体

下载字体

https://www.nerdfonts.com/

可以更换Terminal的配色

https://github.com/mbadolato/iTerm2-Color-Schemes

下载PowerShell7

https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.2

设置每次启动都是启动PowerShell，添加posh的美化脚本到配置文件中

输入

```java
code $profile
```

打开Powershell的配置文件，添加配置

```java
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\jandedobbeleer.omp.json" | Invoke-Expression
```

jandedobbeleer.omp.json为自定义更换的主题

最好是在PowerShell中添加启东市自动清屏的命令

```java
cls
```

可以对常见的工具进行环境变量配置，集成进PowerShell，非常方便

