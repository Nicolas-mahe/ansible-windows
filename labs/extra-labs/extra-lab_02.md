# Configure Dev environment

## Requirements

- Dedicated Azure resource group
- WSL2
- Visual Studio Code + WSL extension

### More Ansible filters and complex loop

1) Create a variable called oscarng_version like below :

```yml
oscarng_versions:
  - version_complete: 7.1.6.24
    metadata:
      version_major: 7
      version_minor: 1
      version_build: 10
      version_release: 32
      date: "18/05/2022"
      time: "13:49:00"
      author: 
        michelin_id: "FX13274"
        name: "DENNERY Maël"
      setups:
        - name: OscarNG 
          group: OscarNG 
          version: 7.1.6.24 
        - name: OscarNGReports 
          group: OscarNG 
          version: 7.0.1.0 
        - name: OscarNGReportUtil 
          group: OscarNG 
          version: 1.0.0.37 
      custom_tasks:
        before:
          - uninstall_telerik
          - cms_check
        after:
          - cms_check
  - version_complete: 7.5.3.14
    metadata:
      version_major: 7
      version_minor: 5
      version_build: 3
      version_release: 14
      date: "18/05/2022"
      time: "13:49:00"
      author: 
        michelin_id: "FX13274"
        name: "DENNERY Maël"
      setups:
        - name: OscarNG 
          group: OscarNG 
          version: 7.5.3.14 
        - name: OscarNGReports 
          group: OscarNG 
          version: 7.0.1.0 
        - name: OscarNGReportUtil 
          group: OscarNG 
          version: 1.0.0.37 
      custom_tasks:
        before:
          - uninstall_telerik
          - uninstall_library
          - cms_check
        after:
          - cms_check
  - version_complete: 7.7.0.0
    metadata:
      version_major: 7
      version_minor: 7
      version_build: 0
      version_release: 0
      date: "18/05/2022"
      time: "13:49:00"
      author: 
        michelin_id: "FX10139"
        name: "Tristan Dugne"
      setups:
        - name: OscarNG 
          group: OscarNG 
          version: 7.7.0.0
        - name: OscarNGReports 
          group: OscarNG 
          version: 7.0.1.0 
        - name: OscarNGReportUtil 
          group: OscarNG 
          version: 1.0.0.37 
      custom_tasks:
        before:
          - uninstall_telerik
          - cms_check
        after:
          - cms_check
```

2) Find the number of entry in the oscarng_versions using the appropriate jinja filter
   
3) Sort the list by major version desc then minor version desc the appropriate jinja filter
   
4) Find custom tasks before for 7.5.3.14 version using the appropriate jinja filter
   
5) Create a task to loop through the oscarng_version list and display the author Michelin ID for each the appropriate jinja filter

### Custom module implemntation

1) Create a library/module directory at your project root
   
2) Copy/Paste the following powershell script :

```ps1
#!powershell

# Copyright: (c) 2022, Maël Dennery <mael.dennery_ext@michelin.com>

#Requires -Module Ansible.ModuleUtils.Legacy

$params = Parse-Args $args -supports_check_mode $false
$diff_mode = Get-AnsibleParam -obj $params -name "_ansible_diff" -type "bool" -default $false

# Module control parameters
$path = Get-AnsibleParam -obj $params -name "path" -type "str" -failifempty ($null -ne $path)
$content = Get-AnsibleParam -obj $params -name "content" -type "str" -failifempty ($null -ne $content)
$state = Get-AnsibleParam -obj $params -name "state" -type "str" -default "present" -validateset 'present','absent'

# Return object 
$result = @{
    created = $false
    changed = $false
    deleted = $false
}

# Actual module actions
if("present" -eq $state)
{
    if(Test-Path -Path $path)
    {
        $currentContent = Get-Content -Path $path
        if($currentContent -ne $content)
        {
            Set-Content -Path $path -Value $content
            $result.changed = $true
        }
    }
    else
    {
        New-Item -Path $path -ItemType "file" -Value $content
        $result.created = $true
    }
}

if("absent" -eq $state)
{
    if(Test-Path -Path $path)
    {
        Remove-Item -Path $path
        $result.deleted = $true
    }
}

Exit-Json -obj $result
```

This script is custom made and is for creating, removing and modifying a file.

3) Try to run the script into your own project (you have to change a bit ansible.cfg)