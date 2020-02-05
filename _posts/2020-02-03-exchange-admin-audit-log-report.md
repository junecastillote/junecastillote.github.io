---
layout: post
title:  "Monitoring and Reporting Exchange Admin Audit Logs"
date:   2020-02-03 00:15:0 +0800
categories: [powershell,Office 365]
author: June Castillote
---
# ExCmdReport Module

This module uses the `Search-AdminAuditLog` Exchange CmdLet under the hood. This can be used to retrieve Exchange Admin Audit Logs from Exchange Online or Exchange Server On-Premises. It uses pagination automatically so that it can retrieve any number of results.

The output can be saved as a pre-formatted HTML file with the option to send as email to specified recipients.

![Sample Email Report](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/images/SampleEmailReport.png)

## Requirements

This module was tested with the following.

* Windows PowerShell 5.1
* Exchange Online (Office 365)
* Exchange Server 2016 (On-Premises).
  * May work with Exchange 2013 and Exchange 2019.
* Remote PowerShell session must be established.
* Exchange Admin Audit Logging must be enabled. Otherwise, there will be no data to return.

## How to Install

### Option 1: Install from PSGallery

`Install-Module ExCmdReport -Scope AllUsers`

### Option 2: Download from GitHub and install manually

Use this if you can't install the module from PSGallery.

1. Download or clone from the [GitHub Repository](https://raw.githubusercontent.com/junecastillote/ExCmdReport).
2. Extract the zip and run `.\InstallMe.ps1` in PowerShell.

![Install Selection](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/images/SampleInstall.png)

## Usage Examples

### Example 1: Get Admin Audit Log Entries

{% highlight powershell %}
<# Get ALL log entries #>
Get-ExCmdLog -searchParamHash @{
    StartDate      = '10/01/2019'
    EndDate        = '10/10/2019'
    ExternalAccess = $false
} -Verbose -resolveAdminName
{% endhighlight %}

![Get Admin Audit Log Entries](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/images/SampleOutput01.png)

### Example 2: Get Admin Audit Log Entries and Send Email Report

{% highlight powershell %}
<# Build report parameters #>
$report = @{
    SendEmail = $true
    From = 'admin@domain.com'
    To = 'user1@domain.com','user2@domain.com'
    smtpServer = 'smtp.office365.com'
    port = 587
    UseSSL = $true
    Credential = (Get-Credential)
    TruncateLongValue = 50
}

<# Get Audit Logs and then send #>
Get-ExCmdLog -searchParamHash @{
    StartDate      = '10/01/2019'
    EndDate        = '10/10/2019'
    ExternalAccess = $false
} -Verbose -resolveAdminName | Write-ExCmdReport @report -Verbose
{% endhighlight %}

![Get Admin Audit Log Entries and Send Email Report](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/images/SampleEmailReport.png)

## Functions

There are four functions included in this version. For details, follow the links below.

* [Get-ExCmdLog](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/Doc/Get-ExCmdLog.md)
* [Write-ExCmdReport](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/Doc/Write-ExCmdReport.md)
* [Connect-ExOP](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/Doc/Connect-ExOP.md)
* [Connect-ExOL](https://raw.githubusercontent.com/junecastillote/ExCmdReport/master/Doc/Connect-ExOL.md)
