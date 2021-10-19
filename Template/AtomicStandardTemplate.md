# Control Name and Applicable standard

## Atomic Test Table of Contents:

- [Control definition](## Control definition)
- [SP-CMM Leveling Guide for Inventory and Control of Enterprise Assets](## SP-CMM leveling Guide for Inventory and Control of Enterprise Assets)
- [Atomics by SP-CMM Level](## Atomics by SP-CMM Level)
  - [Level 1 Atomic Tests](### Level 1 Tests:)
  - [Level 2 Atomic Tests](### Level 2 Tests:)
  - [Level 3 Atomic Tests](### Level 3 Tests:)
  - [Level 4 Atomic Tests](### Level 4 Tests:)
  - [Level 5 Atomic Tests](### Level 5 Tests:)
- [Mappings to applicable control families](#Mappings to applicable control families)
  - [SCF reference](## SCF reference)
- [Additional Resources](## Additional Resources)

## Control definition with sub sections included



## Example of primary control language

## 

### Example of sub control language



## **SP-CMM leveling Guide** [Security & Privacy Capability Maturity Model (SP-CMM)](https://www.securecontrolsframework.com/sp-cmm)



Specific SP-CMM leveling information from the SCF Spread sheet for the specific control 

| SP-CMM Levels                     | Maturity Target |
| --------------------------------- | --------------- |
| CMM 1 – Performed Informally      |                 |
| CMM 2 – Planned & Tracked         |                 |
| CMM 3 – Well-Defined              |                 |
| CMM 4 – Quantitatively Controlled |                 |
| CMM 5 – Continuously Improving    |                 |

## Atomics by SP-CMM Level

### Level 1 Tests:

#### Atomic Test #1 {INSERT NAME}

##### Description

##### **Supported Platforms:**

##### Evidence Commands:

##### Example Output

**<u>Disclaimer:</u>** This example output should be reviewed as a means to understand what the body of evidence auditors would expect to see when reviewing a control. This example should be viewed through such lens and NOT as an example of what will be accepted by all auditors in every circumstance.

### Level 2 Tests:



### Level 3 Tests:



### Level 4 Tests:



### Level 5 tests:



## SCF reference: [Download The Secure Controls Framework (SCF)](https://www.securecontrolsframework.com/download-scf)

## Mappings to applicable control families:



## Additional Resources:





- [Atomic Test #1 - Decode base64 Data into Script](#atomic-test-1---decode-base64-data-into-script)

- [Atomic Test #2 - Execute base64-encoded PowerShell](#atomic-test-2---execute-base64-encoded-powershell)

## Atomic Test #1 - Name

### Description

Upon successful execution, sh will execute art.sh, which is a base64 encoded command, that stdouts `echo Hello from the Atomic Red Team`.

#### **Supported Platforms:** macOS, Linux

#### Attack Commands: Run with `sh`!

```sh
sh -c "echo ZWNobyBIZWxsbyBmcm9tIHRoZSBBdG9taWMgUmVkIFRlYW0= > /tmp/encoded.dat"
cat /tmp/encoded.dat | base64 -d > /tmp/art.sh
chmod +x /tmp/art.sh
/tmp/art.sh
```

## Atomic Test #2 - Execute base64-encoded PowerShell

### Description

Creates base64-encoded PowerShell code and executes it. This is used by numerous adversaries and malicious tools.

Upon successful execution, powershell will execute an encoded command and stdout default is "Write-Host "Hey, Atomic!"

### **Supported Platforms:** Windows

#### Inputs:

| Name               | Description                  | Type   | Default Value             |
| ------------------ | ---------------------------- | ------ | ------------------------- |
| powershell_command | PowerShell command to encode | String | Write-Host "Hey, Atomic!" |

#### Attack Commands: Run with `powershell`!

```powershell
$OriginalCommand = '#{powershell_command}'
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($OriginalCommand)
$EncodedCommand =[Convert]::ToBase64String($Bytes)
$EncodedCommand
powershell.exe -EncodedCommand $EncodedCommand
```

#### **Supported Platforms:** Windows

#### Inputs:

| Name                   | Description                                    | Type   | Default Value                                  |
| ---------------------- | ---------------------------------------------- | ------ | ---------------------------------------------- |
| registry_key_storage   | Windows Registry Key to store code             | String | HKCU:Software\Microsoft\Windows\CurrentVersion |
| powershell_command     | PowerShell command to encode                   | String | Write-Host "Hey, Atomic!"                      |
| registry_entry_storage | Windows Registry entry to store code under key | String | Debug                                          |

#### Attack Commands: Run with `powershell`!

```powershell
$OriginalCommand = '#{powershell_command}'
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($OriginalCommand)
$EncodedCommand =[Convert]::ToBase64String($Bytes)
$EncodedCommand

Set-ItemProperty -Force -Path #{registry_key_storage} -Name #{registry_entry_storage} -Value $EncodedCommand
powershell.exe -Command "IEX ([Text.Encoding]::UNICODE.GetString([Convert]::FromBase64String((gp #{registry_key_storage} #{registry_entry_storage}).#{registry_entry_storage})))"
```

#### Cleanup Commands:

```powershell
Remove-ItemProperty -Force -ErrorAction Ignore -Path #{registry_key_storage} -Name #{registry_entry_storage}
```
