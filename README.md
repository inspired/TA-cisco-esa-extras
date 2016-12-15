# Cisco ESA Extras Add-on for Splunk Enterprise
## Table of Contents

### OVERVIEW

- About the Cisco ESA Extras Add-on for Splunk Enterprise
- Release notes
- Support and resources

### INSTALLATION AND CONFIGURATION

- Hardware and software requirements
- Installation steps
- Deploy to single server instance
- Deploy to distributed deployment
- Deploy to distributed deployment with Search Head Pooling
- Deploy to distributed deployment with Search Head Clustering
- Deploy to Splunk Cloud 
- Configure Cisco ESA Extras Add-on for Splunk Enterprise

### USER GUIDE

- Data types
- Lookups

### OVERVIEW

#### About the Cisco ESA Extras Add-on for Splunk Enterprise

| Author | Mikael Bjerkeland |
| --- | --- |
| App Version | 1.1 |
| Vendor Products | Cisco IronPort ESA C370 on AsyncOS 7.x  or higher |
| Has index-time operations | False |
| Create an index | False |
| Implements summarization | True |

The Cisco ESA Extras Add-on for Splunk Enterprise allows a Splunk® Enterprise administrator to extract and filter event information from Cisco Ironport ESA appliances. The Add-on requires the official Splunk Add-on for Cisco ESA already configured according to Splunk Enterprise Documentation. This Add-on provides extra functionality such AS transactioning events for the Email and Malware data models for CIM compliance.
The Add-on requires customization on your Cisco ESA Ironport Appliance.

This add-on has two functions: 

1. Parses a custom log entry from ESA generated for every attachment and makes it CIM compliant for the Malware data model
2. Runs a saved search that creates a transaction of e-mail logs and AMP logs making them it CIM compliant for the E-mail AND Malware data model (if AMP Malware is detected)


This Add-on was sponsored by a client.

##### Scripts and binaries

No scripts or binaries are included.

#### Release notes

##### About this release

Version 1.1 of the Cisco ESA Extras Add-on for Splunk Enterprise is compatible with:

| Splunk Enterprise versions | 6.x |
| --- | --- |
| CIM | 4.* |
| Platforms | Platform independent |
| Vendor Products | Cisco ESA Extras |
| Lookup file changes |  |

##### New features

Cisco ESA Extras Add-on for Splunk Enterprise includes the following new features:

- Initial release
- CIM compliance
- URL reputation added

##### Fixed issues

Version 1.1 of the Cisco ESA Extras Add-on for Splunk Enterprise fixes the following issues:

- None

##### Known issues

Version 1.1 of the Cisco ESA Extras Add-on for Splunk Enterprise has the following known issues:

- None known

##### Third-party software attributions

Version 1.1 of the Cisco ESA Extras Add-on for Splunk Enterprise incorporates the following third-party software or libraries.

- None

##### Support and resources

**The Cisco ESA Extras Add-on for Splunk Enterprise is supported on a best effort basis. Commercial (paid) support is available by contacting the author.

* Access questions and answers specific to the Cisco ESA Extras Add-on for Splunk Enterprise at http://answers.splunk.com/answers/app/XX

## INSTALLATION AND CONFIGURATION

### Hardware and software requirements

#### Hardware requirements

Cisco ESA Extras Add-on for Splunk Enterprise supports the following server platforms in the versions supported by Splunk Enterprise:

- Windows 7, 8, and 8.1 (64-bit)
- Windows Server 2008, 2008 R2, 2012 and 2012 R2 (64-bit)
- Windows 7, and 8 and 8.1 (32-bit)
- Windows Server 2008 (32-bit)
- 2.6+ kernel Linux distributions (64-bit)
- 2.6+ kernel Linux distributions (32-bit)
- Solaris 10, 11 (64-bit)
- Solaris 10, 11 (SPARC)
- OSX 10.8 (Intel)
- OSX 10.9 (Intel)
- OSX 10.10 (Intel)
- FreeBSD 8, and 9 (64-bit)
- AIX 6.1, 7.1

#### Software requirements

To function properly, Cisco ESA Extras Add-on for Splunk Enterprise requires the following software:

- Splunk Add-on for Cisco ESA
- Optional: Splunk App for Enterprise Security

#### Splunk Enterprise system requirements

Because this add-on runs on Splunk Enterprise, all of the [Splunk Enterprise system requirements](http://docs.splunk.com/Documentation/Splunk/latest/Installation/Systemrequirements) apply.

#### Download

Download the Cisco ESA Extras Add-on for Splunk Enterprise at https://splunkbase.splunk.com/app/TBD

#### Installation steps

To install and configure this app on your supported platform, follow these steps:

1. In your Splunk Enterprise web interface, click on App(s) -> Manage Apps
1. Click on Install app from file
1. Select the file you downloaded, Click Upload, optionally selecting Upgrade app if you are upgrading from an earlier version. Restart Splunk if required

##### Deploy to single server instance

Follow these steps to install the app in a single server instance of Splunk Enterprise:

1. In your Splunk Enterprise web interface, click on App(s) -> Manage Apps
1. Click on Install app from file
1. Select the file you downloaded, Click Upload, optionally selecting Upgrade app if you are upgrading from an earlier version. Restart Splunk if required

##### Deploy to distributed deployment

**Install to search head**

1. In your Splunk Enterprise web interface, click on App(s) -> Manage Apps
1. Click on Install app from file
1. Select the file you downloaded, Click Upload, optionally selecting Upgrade app if you are upgrading from an earlier version. Restart Splunk if required

**Install to indexers**

This app should not be installed on indexers.

**Install to forwarders**

This app should not be installed on forwarders.

##### Deploy to distributed deployment with Search Head Pooling
Not supported
##### Deploy to distributed deployment with Search Head Clustering
Follow the same steps as *Install to search head*.
##### Deploy to Splunk Cloud
Unknown
#### Configure Cisco ESA Extras Add-on for Splunk Enterprise

1. Make sure you already have the official Splunk Add-on for Cisco ESA installed and data coming in to the cisco:esa:textmail and cisco:esa:legacy sourcetypes
2. Log in to your Cisco ESA Ironport Appliance administration web interface and make the following changes:

 1. Create Incoming Content Filter in Cisco Ironport ESA Appliance
  - Name: Whatever you prefer
  - Order: 1
 1. Conditions:
     - Condition: Attachment File Info
     - Rule: attachment-mimetype != "fishpudding/cheese" (SIC, we always want this to be triggered)
    1. Actions:
    - Add Log Entry
     - Rule: 
      `hostname=$Hostname category=mail_with_attachment filtername=$FilterName policy="$Policy" sender=$envelopesender recipient="$EnvelopeRecipients" subject="$Subject" file_name="$filenames" src_int="$RecvInt" receiving_listener="$RecvListener" src_ip=$RemoteIP src_host=$remotehost x-ironport-AV=$Header['X-IronPort-AV']`
   
  1. Mail Policies: Content Filters
  - Enable Content Filters
  - Add the filter you created to the policy you desired (i.e. Default Policy).
  - Tick Enable box

3. Create an index in Splunk called esa_summary OR create a copy of default/macros.conf in local/macros.conf replacing index=esa_summary with index=summary

4. Send some malware e-mails through your ESA appliance and search for "Custom Log Entry" in Splunk. You should get back results

## USER GUIDE

### Data types

This app provides search-time knowledge for the following types of data from Cisco Ironport ESA:

**Search-time**

- cisco:esa:summary:email - Summarized Cisco ESA E-mail transactions
- cisco:esa:legacy - Cisco ESA AMP Malware events
- cisco:esa:textmail - Cisco ESA Malware events

These data types support the following Common Information Model data models:

| Source Type | CIM Data Models |
| --- | --- |
| cisco:esa:summary:email | Email<br/>Malware<br/> |
| cisco:esa:legacy | Malware |
| cisco:esa:textmail | Malware |


### Lookups

The Cisco ESA Extras Add-on for Splunk Enterprise contains no lookup files.
