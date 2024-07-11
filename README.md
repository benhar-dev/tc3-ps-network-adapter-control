# Network Adapter Control using Tc3, NT_StartProcess and Powershell

## Disclaimer

This is a personal guide not a peer reviewed journal or a sponsored publication. We make
no representations as to accuracy, completeness, correctness, suitability, or validity of any
information and will not be liable for any errors, omissions, or delays in this information or any
losses injuries, or damages arising from its display or use. All information is provided on an as
is basis. It is the reader’s responsibility to verify their own facts.

The views and opinions expressed in this guide are those of the authors and do not
necessarily reflect the official policy or position of any other agency, organization, employer or
company. Assumptions made in the analysis are not reflective of the position of any entity
other than the author(s) and, since we are critically thinking human beings, these views are
always subject to change, revision, and rethinking at any time. Please do not hold us to them
in perpetuity.

## Overview

This is a proof of concept that you can use NT_StartProcess (with powershell) to enable and disable a network adapter.

## Code Snippets

As with my previous example of tc3-ps-ping, we build the application with NT_Startprocess. This application does not require feedback so this greatly simplifies the task. To see how to return values from NT_Startprocess then check out [this](https://github.com/benhar-dev/tc3-ps-ping) repo for ideas.

Building the layers of this example, we start with the program we will execute. First, the code we will create in Powershell.

```powershell
Disable-NetAdapter -Name "X001" -Confirm:$false
```

```powershell
Enable-NetAdapter -Name "X001" -Confirm:$false
```

The above code will enable or disable X001 depending on which commands are used. For the examples below only the Disable will be used for simplicity.

To execute this on cmd you can use

```bash
powershell -c Disable-NetAdapter -Name "X001" -Confirm:$false;
```

We could now execute this directly using NT_StartProcess but this would causes the powershell window to pop-up which is not ideal.

In order to hid this we can use mshta to execute vbscript directly from the command line.

```
C:\Windows\System32\mshta vbscript:Execute(<<vb script goes here>>)
```

So combining the whole command together results in the following.

```bash
C:\Windows\System32\mshta vbscript:Execute("CreateObject(""WScript.Shell"").Run ""powershell -c Disable-NetAdapter -Name """"X001"""" -Confirm:$false;"", 0: window.close")
```

The final step was to escape the $ using $$ in TwinCAT. From this point the command is compatible with NT_StartProcess.

```bash
Command : T_MAXSTRING := 'C:\Windows\System32\mshta vbscript:Execute("CreateObject(""WScript.Shell"").Run ""powershell -c Disable-NetAdapter -Name """"X001"""" -Confirm:$$false;"", 0: window.close")';
```

## Versions

- TcXaeShell 3.1.4024.55
- Powershell
- mshta

## Need more help?

Please visit http://beckhoff.com/ for further guides

Coding byte video series found [here](https://codingbytes.teachable.com/p/codingbytes)
