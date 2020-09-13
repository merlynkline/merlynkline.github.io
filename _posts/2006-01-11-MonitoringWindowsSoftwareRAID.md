---
layout: post
title:  "Monitoring Windows software RAID"
date:   2006-01-11
categories: Windows archaeology
---
*(While setting up this site in September 2020 I went looking for articles to add to it from my scattered archives. This is one that I came across that probably has little relevance any more but is perhaps an interesting bit of history.)*

Microsoft's "server class" operating systems have a useful software RAID feature that allows you to use multiple disk drives as a single volume in various ways to obtain protection from disk failure through redundancy (among other things). The problem is that there seems to be no way to know when a disk has failed except to log in and check the status manually. This means that when one drive in a redundancy group fails things carry on as they're supposed to until the next drive fails and then you lose everything. So there's not really much protection unless you frequently do manual checks of the status so that you can replace failed drives in a timely fashion. For a system administrator responsible for a large number of servers this is a real chore.

I devoted some effort to researching this and found that there is very little available in the way of useful solutions to this problem although there is a small number over-priced network management packages available, all laden with unwanted features. I therefore set about devising a solution of my own and soon discovered that this is easier said than done - not only do Microsoft (incredibly) not include any automatic RAID monitoring software in their "server class" operating systems, they also don't apparently provide any easily accessible API that could be used for the purpose either. The sprawling WMI system describes such things but they seem not to be implemented. Given the supposed application of these systems I find this extraordinary and to this day I still have the uncomfortable feeling that I must be missing something glaringly obvious. However, after devoting more than enough time to trying to find a solution I decided that I had acquired enough information to be able to hack up something that would be adequate and would be more cost effective than further research.

My solution revolves around a command-line utility called `diskpart.exe`. I have constructed VBScript and `.BAT` wrappers for this utility which allow it to be used for automated RAID monitoring. At the time of writing I have seen this work with just one, artifically induced, RAID failure so for the time being I am continuing manual monitoring, though at a lower frequency, until I have more confidence.

My implementation of this solution is for Windows 2000. It should work for other versions of Windows that diskpart works on but the batch file may need tweaking because I have assumed the `diskpart.exe` has been downloaded and installed in a directory along with the other files described here. This is what you need for Windows 2000:

* `diskpart.exe` - the core utility.
* `dpscript.txt` - a simple script for the diskpart utility.
* `dprun.bat` - a small batch file wrapper for the diskpart tool.
* `volcheck.vbs` - the VBScript wrapper that calls dprun.bat and parses the output.

These files are described in detail below. You should assemble them all in a single directory, e.g. `C:\RAIDCheck`, and then set up a scheduled task to call the VBScript wrapper on a regular and frequent basis (at least daily), like this: `CScript /NoLogo "C:\RAIDCheck\volcheck.vbs"`

Note that the script creates a text file from the output of the diskpart utility, which it then parses. This means that it will need write access to the directory in which all these files reside. I assume that you need administrator rights to run the diskpart utility in any case but I haven't checked this. I would suggest using an administrator account to run the utility when setting up the scheduled task.

NB: in order to run diskpart, the built-in user SYSTEM must have access rights to some DCOM applications (I don't know exactly which ones but they include the Logical Disk Manager). This caused me a problem where diskpart would fail with an error like `DISKPART.EXE - Application Error : The instruction at "0x006a0183" referenced memory at "0x886a6b79". The memory could not be "written".` and the event log would show an access denied error from LDM. If you see this problem then run `DCOMCnfg` and under the "Default Security" tab, click the "Edit Default..." button in the "Default Access Permissions" section. If the list of users and groups allowed access is non-blank and does not include the user SYSTEM then you must add it.

I suggest testing your initial installation by modifying the `volcheck.vbs` script to change the word "Healthy" to something else. This should cause disk status reports to be emailed to you whenever the scheduled task runs, which verifies that everything is installed and configured properly. Once you're happy with that, restore the script to its original state and your monitoring should be good. Ideally you should then force various failure modes of your RAID arrays to ensure that these will all be detected.

That's it. No guarantees, as usual. Good luck!

Merlyn Kline  
January 2006

----

`diskpart.exe`  
This is the core utility. For Windows 2000 it needs to be downloaded from Microsoft's web site. I found it at http://www.microsoft.com/windows2000/techinfo/reskit/tools/new/diskpart-o.asp but it may have moved since then. For simplicity I copied the executable into the directory with the other files described here but you could keep it elsewhere, in which case you will need to modify the dprun.bat file or just make sure that diskpart is in your system path when the scheduled task runs.

----

`dpscript.exe`  
The diskpart utility needs a script so we don't have to interact with it at the command line. This is just a text file containing a single line, as follows:
`list volume`

----

`dprun.bat`  
I created this batch file wrapper for the diskpart utility because I couldn't see how do do I/O redirection from within the VBScript wrapper and I lost patience trying to work it out. It's very simple:

```
@echo off
%1
cd "%2"
diskpart /s dpscript.txt > dpout.txt
```

Note that it creates the file `dpout.txt` in our working directory.

----

`volcheck.vbs`  
This is the main script which calls the diskpart utiltity, parses the output and emails the administrators if a problem is detected. As implemented it requires access to an SMTP server to handle the email for it. You will need to set the two constants at the top of the script, one being the email address to send reports to and the other being the name of the SMTP server to use for this purpose. If you have a different email environment (e.g. MS Exchange) then you will need to change the `SendEmail` subroutine. Alternatively you may wish to change it to perform some other type of notification if email is not appropriate for you.

``` VBScript
' VBScript to check the health of drives.
'
' Merlyn Kline
' Jan 2006
'
Option Explicit
'----------------------------------------------------------------------------
' Global constants
Const strVersion = "1.0"

Const strEmail     = "sysadmin@your.domain" ' Email address for reports
Const strSMTPHost  = "some.smtp.server"     ' The name of an SMTP server that will forward mail for you

Dim CRLF : CRLF = Chr(13) & Chr(10)

'----------------------------------------------------------------------------
' Utility subroutines

'-----------------------
' Send an email
Sub SendEmail(strTo, strSubj, strText)
  Set message = CreateObject("CDO.Message")
  message.subject = strSubj
  message.from = strEmail & " (" & WScript.ScriptName & "v " & strVersion & " on " & ScriptComputer & ")"
  message.to = strTo
  message.textbody = strText
  message.Configuration.Fields.Item("http://schemas.microsoft.com/cdo/configuration/sendusing") = 2
  message.Configuration.Fields.Item("http://schemas.microsoft.com/cdo/configuration/smtpserver") = strSMTPHost
  message.Configuration.Fields.Update
  message.send
  Set message = Nothing
End Sub

'-----------------------
' Get drive letter this script is on (returns blank if none is available, e.g. in a network path)
Function ScriptDrive
  Dim strP, strRes
  
  strP = WScript.ScriptFullName
  If Mid(strP,2,1) = ":" Then strRes = Left(strP,1)
  ScriptDrive = strRes
End Function

'-----------------------
' Get the path to directory this script is in (no drive letter or trailing \)
Function ScriptPath
  Dim strRes
  
  strRes = WScript.ScriptFullName
  If Mid(strRes,2,1) = ":" Then strRes = Mid(strRes,3)
  ScriptPath = Left(strRes,InStrRev(strRes,"\")-1)
End Function

'-----------------------
' Get the name of the computer this script is on
Function ScriptComputer
  Dim strRes, objNet
  
  Set objNet = WScript.CreateObject("WScript.Network")
  strRes = objNet.UserDomain & "\" & objNet.ComputerName
  ScriptComputer = strRes
End Function


'----------------------------------------------------------------------------
'----------------------------------------------------------------------------

'-----------------------
' Get and interpret a drive report. Return an emtpy string if no problems, otherwise return the whole report
Function CheckVolumes
  Dim strRes, strCMD, objShell, iStatus, fso, strPath, textfile, bStarted, strStatus, strReport, bError
  
  Set objShell = WScript.CreateObject("WScript.Shell")
  strCMD = """" & ScriptDrive & ":" & ScriptPath & "\dprun.bat"" " & ScriptDrive & ": """ & ScriptPath & """"
  iStatus = objShell.Run(strCMD,0,true)
  If iStatus<>0 Then
    strRes = "ERROR: diskpart.exe returned error code " & iStatus
  Else
    Set fso = CreateObject("Scripting.FileSystemObject")
    strPath = ScriptDrive & ":" & ScriptPath & "\dpout.txt"
    If Not fso.FileExists(strPath) Then
      strRes = "diskpart output file '" & strPath & "' not found"
    Else
      Set textfile = fso.OpenTextFile(strPath ,1)
      bStarted = False
      bError = True
      strReport = ""
      Do While (Not textfile.AtEndOfStream)
        strLine = textfile.ReadLine
        strReport = strReport & strLine & CRLF
        If bStarted Then
          If strLine<>"" Then
            strStatus = Mid(strLine,61,9)
            If strStatus<>"Healthy  " And strStatus<>"         " Then bError = 1
          End If
        Else
          If strLine = "  ----------  ---  -----------  -----  ----------  -------  ---------  --------" Then bStarted = True : bError = False
        End If
      Loop
      If bError Then strRes = strReport
      If Not bStarted Then strRes = "**UNRECOGNISED REPORT FORMAT**" & CRLF & CRLF & strRes
    End If
  End If
  CheckVolumes = strRes
End Function


Dim strReport
strReport = CheckVolumes
If strReport <> "" Then SendEmail strEmail, ScriptComputer & " drive PROBLEM report", strReport
```