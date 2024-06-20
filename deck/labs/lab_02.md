# Configure testing environement (Azure)

## Goals:

- Create a Windows Server 2022 VM
- Configure Winrm on the VM (We will use port 22 instead of 5986 for winrm)

## Instructions

1) Go to https://portal.azure.com and follow the trainer's instructions to create a new Windows VM

2) Enable Winrm on the VM: Files to download and copy on the VM

- https://github.com/Nicolas-mahe/ansible-windows/blob/dev/deck/labs/tools/winrm/ConfigureWinRM.ps1
- https://github.com/Nicolas-mahe/ansible-windows/blob/dev/deck/labs/tools/winrm/makecert.exe

3) Run the script `ConfigureWinRM.ps1` on the VM: Open a CMD terminal with admin rights and run the following command:

```bash
powershell -ExecutionPolicy Unrestricted -File ConfigureWinRM.ps1
```

## Result

Now you have a windows-server vm ready to be used by ansible :clap:
