Windows Scripting Host (WSH)
--
它是一个Windows本机引擎，cscript.exe（用于命令行脚本）和wscript.exe（用于UI脚本），负责执行各种Microsoft Visual Basic脚本（VBScript），包括vbs和vbe。
需要注意的是，Windows 操作系统上的 VBScript 引擎以与普通用户相同级别的访问和权限来运行和执行应用程序；因此，它对红队队员很有用。
现在让我们编写一个简单的 VBScript 代码来创建一个 Windows 消息框，显示欢迎使用 THM 消息。确保将以下代码保存到文件中，例如 hello.vbs.
```javascript
Dim message 
message = "Welcome to THM"
MsgBox message
```
在第一行中，我们使用 Dim 声明了消息变量。然后我们在消息变量中存储一个字符串值Welcome to THM。在下一行中，我们使用 MsgBox 函数来显示变量的内容。有关 MsgBox 函数的更多信息，请访问此处。然后，我们使用wscript来运行并执行hello.vbs的内容，结果，将弹出一条Windows消息，其中包含Welcome to THM消息。
现在让我们使用 VBScript 来运行可执行文件。下面的vbs代码是调用Windows计算器，证明我们可以使用Windows本机引擎（WSH）执行.exe文件。
```javascript
Set shell = WScript.CreateObject("Wscript.Shell")
shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName),0,True
```
我们使用 CreateObject 创建 WScript 库的对象来调用执行负载。然后，我们利用 Run 方法来执行有效负载。对于此任务，我们将运行 Windows 计算器calc.exe。
要执行 vbs 文件，我们可以使用 wscript 运行它`
`c:\Windows\System32>wscript c:\Users\thm\Desktop\payload.vbs`
`c:\Windows\System32>cscript.exe c:\Users\thm\Desktop\payload.vbs
另一个技巧。如果VBS文件被列入黑名单，那么我们可以将文件重命名为.txt文件并使用wscript运行它
`c:\Windows\System32>wscript /e:VBScript c:\Users\thm\Desktop\payload.txt`


