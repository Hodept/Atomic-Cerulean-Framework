# CIS Control 3 - Data Protection

## [CIS Controls v8 - (153)]([CIS Controls Navigator](https://www.cisecurity.org/controls/cis-controls-navigator/))

> Develop processes and technical controls to identify, classify, securely handle, retain, and dispose of data.

## 3.4 [Enforce Data Retention](https://www.cisecurity.org/controls/cis-controls-navigator/#collapse-a16)

> Retain data according to the enterprise's data management process. Data retention must include both minimum and maximum timelines.

## Atomic Tests

- Atomic Test #1 - Splunk Data Retention Policy

## Atomic Test #1 - Splunk Data Retention Policy

### Description

The indexer stores indexed data in directories called **[buckets](https://docs.splunk.com/Splexicon:Bucket "Splexicon:Bucket")**. Buckets go through four stages of retirement. When indexed data reaches the final, frozen state, the indexer removes it from the index. You can configure the indexer to archive the data when it freezes, instead of deleting it entirely. See ["Archive indexed data"](http://docs.splunk.com/Documentation/Splunk/8.2.2/Indexer/Automatearchiving) for details.

You can use the age of data to determine when a bucket gets rolled to frozen. When the most recent data in a particular bucket reaches the configured age, the entire bucket is rolled.

#### **Supported Platforms:** Splunk

#### Evidence Commands: Run with SPL [(**Splunk's Search Processing Language**)]((https://www.splunk.com/en_us/resources/search-processing-language.html))

```SPL
| rest /services/data/indexes 
| where disabled = 0 
| search NOT title = "_*" 
| eval currentDBSizeGB = round( currentDBSizeMB / 1024) 
| where currentDBSizeGB > 0 
| table splunk_server title summaryHomePath_expanded minTime maxTime currentDBSizeGB totalEventCount frozenTimePeriodInSecs coldToFrozenDir maxTotalDataSizeMB 
| rename minTime AS earliest maxTime AS latest summaryHomePath_expanded AS index_path currentDBSizeGB AS index_size totalEventCount AS event_cnt frozenTimePeriodInSecs AS index_retention coldToFrozenDir AS index_path_frozen maxTotalDataSizeMB AS index_size_max title AS index
```

## Example Output

**<u>Disclaimer:</u>** This example output should be reviewed as a means to understand what the body of evidence auditors would expect to see when reviewing a control. This example should be viewed through such lense and NOT as an example of what will be accepted by all auditors in every circumstance.

# Mappings to applicable control families:

### **CSA Cloud Controls Matrix v4 Groups**

### DSP-16

Data Retention and Deletion - Data retention, archiving and deletion is managed in accordance with business requirements, applicable laws and regulations.

### **CMMC Groups**

### AM.3.036

Define procedures for the handling of CUI data.

### **MITRE Enterprise ATT&CK v8.2 Groups**

### T1070

Indicator Removal on Host - Adversaries may delete or alter generated artifacts on a host system, including logs or captured files such as quarantined malware. Locations and format of logs are platform or product-specific, however standard operating system logs are captured as Windows events or Linux/macOS files such as Bash History and /var/log/*.

### T1070.001

Clear Windows Event Logs - Adversaries may clear Windows Event Logs to hide the activity of an intrusion. Windows Event Logs are a record of a computer's alerts and notifications. There are three system-defined sources of events: System, Application, and Security, with five event types: Error, Warning, Information, Success Audit, and Failure Audit.

### T1070.002

Clear Linux or Mac System Logs - Adversaries may clear system logs to hide evidence of an intrusion. macOS and Linux both keep track of system or user-initiated actions via system logs. The majority of native system logging is stored under the /var/log/ directory. Subfolders in this directory categorize logs by their related functions.
