---
layout: post
title:  "How to Generate DNS MX Records Report using PowerShell"
author: june
categories: [PowerShell]
tags: [script,dns,report]
image: assets/images/dns-record-report.jpg
description: "Learn how to create a DNS report of your MX records to monitor their validity using PowerShell."
featured: false
hidden: false
toc: true
comments: true
---

> *Originally posted on Augist 18, 2018*

When you manage an email system, you need to make sure that your organizations MX records are valid. It is but essential to be able to generate a report of all the DNS MX Records for your domains to monitor their validity or availability.

Being caught by surprise as your MX records go missing resulting in incoming mail delivery failures is never good.

This script can query a list of domains for their MX records, and generate a report which can be also sent as an email. Depending on your purpose, you can just run it manually or setup a task to run the script at an interval or daily schedule.

## Requirements

This script requires PowerShell version 5.1
## Where to Get the Script

### From GitHub Repository
You can download the latest version of the script from this link, and just save it in you preferred folder.
[Get-MXReport](https://github.com/junecastillote/Get-MXReport)

### From PS Gallery
This script can be installed from PSGallery by running this command:

```PowerShell
Install-Script Get-MXReport –Scope AllUsers
```

Once completed, the script will be installed in *C:\Program Files\WindowsPowerShell\Scripts* by default, and is added to the PATH.

## How to Use the Script

```PowerShell
Get-MXReport.ps1
[-domains] <string[]>
[-outputDirectory] <string>
[[-nameServer] <string>]
[[-logDirectory] <string>]
[[-headerPrefix] <string>]
[[-sendEmail] <string>]
[[-sender] <string>]
[[-recipients] <string[]>]
[[-smtpServer] <string>]
[[-smtpPort] <string>]
[[-smtpCredential] <pscredential>]
[-smtpServerRequiresAuthentication]
[-smtpSSL]
[-attachCSVReport]
[<CommonParameters>]
```

## Examples

### EXAMPLE 1

This example queries the MX record for the specified domains and save the HTML and CSV report to the specified folder

```PowerShell
Get-MXReport.ps1 –domains 'gmail.com','lazyexchangeadmin.com' –outputDirectory C:\Scripts\output
```

### EXAMPLE 2

This example queries the MX records and save the reports to the specified folder. If a failed lookup is logged, an email report is sent to the specified recipients using a non-authenticated SMTP relay with the CSV report attached.

```PowerShell
$domainsList = "gmail.com","yahoo.com","lazyexchangeadmin.com"

Get-MXReport.ps1 –domains $domainsList –outputDirectory C:\Scripts\output –sendEmail OnError –sender sender@domain.com –recipients recipient1@domain.com,recipient2@domain.com –smtpServer smtp.server.com –smtpPort 25 –AttachCSVReport
```

### EXAMPLE 3

This example queries the MX records, save the reports to the specified folder and if a failed lookup is logged, it will send the email report to the specified recipient using an authenticated SMTP relay via Office 365, port 587 with SSL.

```PowerShell
$domainsList = "gmail.com","yahoo.com","lazyexchangeadmin.com"
$smtpCredential = (Get-Credential)
Get-MXReport.ps1 –domains $domainsList –outputDirectory C:\Scripts\output –sendEmail OnError –sender sender@domain.com –recipients recipient1@domain.com,recipient2@domain.com –smtpServer smtp.office365.com –smtpPort 587 –smtpSSL –AttachCSVReport –smtpServerRequiresAuthentication –smtpCredential $smtpCredential
```

## Required Parameters
-domains
Type: String (multiple, array)
Description: List of mail domains to query.
Example: "contoso.com","gmail.com"
-outputDirectory
Type: String (directory path)
Description: Path to where the reports will be saved.
Example: C:\scripts\report

## Optional Parameters
-nameServer
Type: String
Description: Name or IP of the custom DNS Server to use for lookup. If not specified, script will use the local DNS Server IP of the machine.
Example: "8.8.8.8"
-logDirectory
Type: String
Description: Path to where the session transcript log should be saved. Logging will only work if this is specified.
Example: C:\scripts\log
-headerPrefix
Type: String
Description: String that will appear in front of the report title or email subject. Can be any string value.
Example: MyCompanyName
-sendEmail
Type: String
Valid values:
Always – Send email report everytime.
OnError – Send email report only when there’s a failed lookup result.
Description: If specified, the script will trigger sending of the report through email.
Example: –sendEmail Always, –sendEmail OnError
-sender
Type: String
Description: The email address that will appear as the sender of the email
Example: sender@domain.com
-recipients
Type: String
Description: The recipient address or addresses of the email report. This parameter is required if -sendEmail is used.
Example: recipient1@domain.com,recipient2@domain.com
-smtpServer
Type: String
Description: The IP address, Hostname, or Fully-Qualified Domain Name of the SMTP Relay Server. This parameter is required if -sendEmail is used.
Example: smtp.server.com
-smtpPort
Type: Integer
Description: The SMTP Port Number to be used along with the –smtpServer. This parameter is required if -sendEmail is used.
Example: 25
-smtpServerRequiresAuthentication
Type: Switch
Description: Use if the SMTP server requires authentication to be able to relay messages. There is no need to specify any value for this parameter.
-smtpCredential
Type: PSCredential
Description: The login credential for the SMTP server authentication. This parameter is required if -smtpServerRequiresAuthenticationis used.
Example: $smtpCredential = Get-Credential
-smtpSSL
Type: Switch
Description: Use this switch if the SMTP Relay connection requires SSL.

Sample Output
HTML Email Summary Report



CSV Report

