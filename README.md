# amsi-tracer
Leverage AMSI (Antimalware Scan Interface) technology to aid your analysis.

This tool saves all buffers (scripts, .NET assemblies, etc) passed into AMSI during dynamic execution.

![Demo (WSHRAT)](wshrat_demo.gif)

## Basic overview
AMSI is (originally) intended for application & AV vendors to interact with one another through a standard interface. Out of the box, it is integrated into the following Windows components for scanning:
* Powershell/JScript/VBScript (scripts, interactive use, and dynamic code evaluation)
* [Office macros](https://docs.microsoft.com/en-us/windows/win32/amsi/how-amsi-helps#amsi-integration-with-javascriptvba)
* .NET Framework 4.8 ([in-memory loading via Assembly.Load](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-8-early-access-build-3694/#runtime-antimalware-scanning-for-all-assemblies))
* Windows Management Instrumentation (WMI)
* ...

As malware (Windows) usually depend on one of these components within their execution chain, especially early on (e.g. downloader/dropper via maldoc), we can leverage this tool while dynamically analyzing a malware to unearth obfuscated code execution, dump assemblies that are dynamically loaded, and more.

This tool is an alternative solution to using event tracing (ETW) to get AMSI events, which may be cumbersome and not easily integrable into automated pipelines (e.g. sandboxes).

Once installed, the dumps are found at ```C:\amsi_tracer``` and have the following naming convention ```<epoch>_<processName>_<processId>_<threadId>_<counter>.dmp```

## Installation
**_AMSI is only available on Windows 10 / Server 2016 and above_**

Either modify/build the project from scratch or download the latest (x86/x64) builds to get the AMSI provider dll.

Ensure Visual C++ redist packages (2015-2019) are installed ([x86](https://aka.ms/vs/16/release/vc_redist.x86.exe) | [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)).

Register the AMSI provider:
```batch
(elevated cmd) $ regsvr32.exe amsi-tracer.dll
```

To uninstall:
```batch
(elevated cmd) $ regsvr32.exe /u amsi-tracer.dll
```

### Office Integration
You need to have Office 2016 (and above) or Office 365 installed.

Enable AMSI for all documents by setting the following registry key (Office 2016 example):
```
[HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\Common\Security]
"MacroRuntimeScanScope": (dword) 0x00000002
```

## Examples
Here are some sample outputs:

[WSHRAT - JScript](./Examples/wshrat_js_bf110a79e1751b7432b1505e860d3a2cbd702e16292420088b8b1b5c7c5a4256_js.7z)

[Babax/Osno Stealer/Ransomware (XLS -> Powershell -> .NET)](./Examples/babax_osno_stealer_ransomware_xls_a945eae89740b354233e7dfb2157169c29d43cc08fc623a76b6fae3fba783e82.7z)

Password: ```infected```
