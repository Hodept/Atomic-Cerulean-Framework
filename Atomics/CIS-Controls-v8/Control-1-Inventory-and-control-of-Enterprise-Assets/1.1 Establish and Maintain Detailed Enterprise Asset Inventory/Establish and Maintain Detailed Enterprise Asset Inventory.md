# CIS Control 1 - Inventory and Control of Enterprise Assets

## [CIS Controls v8 - (153)]([CIS Controls Navigator](https://www.cisecurity.org/controls/cis-controls-navigator/))

> Actively manage (inventory, track, and correct) all enterprise assets (end-user devices, including portable and mobile; network devices; non-computing/Internet of Things (IoT) devices; and servers) connected to the infrastructure physically, virtually, remotely, and those within cloud environments, to accurately know the totality of assets that need to be monitored and protected within the enterprise. This will also support identifying unauthorized and unmanaged assets to remove or remediate.

## 1.1 [Establish and Maintain Detailed Enterprise Asset Inventory](https://www.cisecurity.org/controls/cis-controls-navigator/#collapse-a1)

> Establish and maintain an accurate, detailed, and up-to-date inventory of all enterprise assets with the potential to store or process data, to include: end-user devices (including portable and mobile), network devices, non-computing/IoT devices, and servers. Ensure the inventory records the network address (if static), hardware address, machine name, enterprise asset owner, department for each asset, and whether the asset has been approved to connect to the network. For mobile end-user devices,MDM type tools can support this process, where appropriate. This inventory includes assetsconnected to the infrastructure physically, virtually, remotely, and those within cloud environments. Additionally, it includes assets that are regularly connected to the enterprise's network infrastructure, even if they are not under control of the enterprise. Review and update the inventory of all enterprise assets bi-annually, or more frequently.

## Atomic Tests

- [Atomic Test #1 - Splunk Ad-Mon](#Atomic Test #1 - Splunk Ad-Mon)

- [Atomic Test #2 - Execute base64-encoded PowerShell](#atomic-test-2---execute-base64-encoded-powershell)

## Atomic Test #1 - Splunk Ad-Mon

### Description

You can configure AD monitoring to watch for changes to your Active Directory forest and collect user and machine metadata. You can use this feature combined with dynamic list lookups to decorate or modify events with any information available in AD. See [About lookups](http://docs.splunk.com/Documentation/SplunkCloud/latest/Knowledge/Aboutlookupsandfieldactions) in the *Knowledge Manager Manual*.

After you configure Splunk Enterprise to monitor your Active Directory, it takes a baseline snapshot of the AD schema. It uses this snapshot to establish a starting point for monitoring.

The AD monitoring input runs as a separate process called `splunk-admon.exe`. It runs once for every Active Directory monitoring input you define in Splunk Enterprise.

[Monitor Active Directory - Splunk Documentation](https://docs.splunk.com/Documentation/SplunkCloud/8.2.2107/Data/MonitorActiveDirectory)

#### **Supported Platforms:** Windows

#### Evidence Commands: Run with SPL [(**Splunk's Search Processing Language**)]((https://www.splunk.com/en_us/resources/search-processing-language.html))

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
