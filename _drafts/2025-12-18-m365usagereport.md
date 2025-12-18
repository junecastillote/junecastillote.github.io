---
layout: post
title:  "How to Generate Microsoft 365 Usage Reports in PowerShell"
author: june
categories: [How-To,Microsoft 365,Script,PowerShell]
tags: [powershell,admin,report]
image: assets/images/m365usagereport/preview.png
description: "Learn how to generate Microsoft 365 usage reports in PowerShell with HTML and email output using the PS.M365UsageReport module"
featured: false
hidden: false
toc: true
comments: true
---

The Microsoft 365 admin portal offers a Usage Report view that provides a quick glance at service activity across your organization. While convenient for high-level insights, it falls short when you need a detailed, comprehensive report covering all services, or when you need to automate reporting on a regular schedule.

In this post, we'll show you how to generate a complete Microsoft 365 usage report using PowerShell. Leveraging the [Microsoft 365 Usage Report PowerShell module](https://github.com/junecastillote/PS.M365UsageReport), you can create an HTML-based report with the option to include raw CSV data. This approach is ideal for scheduled reporting, auditing, or sharing with stakeholders via email—providing a level of flexibility and detail that the portal alone cannot offer.

## Overview

This PowerShell module exports Microsoft 365 usage reports using the [*Microsoft Graph API*](https://docs.microsoft.com/en-us/graph/overview?view=graph-rest-1.0) and the [*Exchange Online Management PowerShell Module*](https://www.powershellgallery.com/packages/ExchangeOnlineManagement). The results are saved locally and can also be sent by email.

The reports that can be exported using this script are:

- Assigned Licenses Report Count
  - Exchange
  - SharePoint
  - OneDrive
  - Skype for Business
  - Teams
  - Yammer
- Microsoft 365
  - Users
  - Activations
- Exchange Reports
  - Mailbox Status (Active/Inactive)
  - Mailbox Provisioning (Created/Deleted)
  - Mailbox Quota
  - Email App Usage
  - Office 365 Groups Provisioning
  - Mail Traffic (Inbound/Outbound)
  - Spam and Malware Detection (Count)
  - ATP Mail Detection (ATP Safe Links, ATP Safe Attachments)
  - Top 10 Mail Senders
  - Top 10 Mail Recipients
  - Top 10 Spam Recipients
  - Top 10 Malware Recipients
  - Top 10 Malware Detected
- SharePoint Reports
  - Total, Active, Inactive Sites
  - Storage Used (Tenant)
- OneDrive Reports
  - Total, Active, Inactive Sites
  - Storage Used (Tenant)
- Microsoft Teams
  - Total Users
  - Active Users
  - Inactive Users
  - Guest Users
  - Total 1:1 Calls
  - Total Channel Messages
  - Total Private Chat Messages
  - Total Meetings
  - Total Audio Duration (minutes)
  - Total Video Duration (minutes)
  - Total Screen Sharing Duration (minutes)
  - Device Usage Distribution

## Requirements

- Windows PowerShell or PowerShell 7+.
- The [*ExchangeOnlineManagement PowerShell Module*](https://www.powershellgallery.com/packages/ExchangeOnlineManagement/)
- The [Microsoft Graph PowerShell Module](https://learn.microsoft.com/en-us/powershell/microsoftgraph/get-started?view=graph-powershell-1.0)

### For Automation and Unattended Jobs

- A registered Azure AD (OAuth) App with the following settings:

    > Annex: Register a New Azure AD App
    >
  - **API**: *Microsoft Graph*
    - **Permission Type**: *Application*
    - **Permission(s)**:
      - `Reports.Read.All` - For reading the usage reports.
      - `Directory.Read.All` - For getting the deleted Microsoft 365 Groups and users.
      - `Mail.Send` - For sending the report by email.
  - **API**: *Office 365 Exchange Online*
    - **Permission Type**: *Application*
    - **Permission(s)**:
      - `Exchange.ManageAsApp` - Exchange Online app-only access.
- A new or existing Exchange Online mailbox to use as the report email sender. This can be a shared mailbox without a license.

### For Manual and Interactive Use

- The logged-in user must be an Exchange administrator.
- The logged-in user must have Entra access to read the directory and reports, and have a valid Exchange Online mailbox if they plan to send the report by email.

## How to Get the Module

[*Clone*](https://github.com/junecastillote/PS-Microsoft-365-Usage-Report.git) or [*download*](https://github.com/junecastillote/PS-Microsoft-365-Usage-Report/archive/refs/heads/main.zip) the script from the [*repository*](https://github.com/junecastillote/PS-Microsoft-365-Usage-Report). After downloading, extract the files to your preferred location.

You can also install the module from [PSGallery](https://www.powershellgallery.com/packages/PS.M365UsageReport).

```powershell
# Current user
Install-Module PS.M365UsageReport

# All users (requires admin)
Install-Module PS.M365UsageReport -Scope AllUsers
```

## The New-M365UsageReport Command

This module contains several functions, but `New-M365UsageReport` is the main function to generate the report.

### Syntax

```powershell
New-M365UsageReport [[-ReportPeriod] [[-Scope] [[-Exclude] [[-From] [[-To] [[-Cc] [[-Bcc] [[-CustomEmailSubject] [-SendEmail] [-ShowReport] [<CommonParameters>]
```

### Parameters

The `New-M365UsageReport` function has these parameters.

- `ReportPeriod` - Indicates the reporting period. Valid values are `7, 30, 90, 180`. If not used, the default value is `7`.
- `Scope` - Specifies the scope of the report (which services/workloads). If not used, the report will include all available workload. This accepts multiple values. Valid values are:
  - `Microsoft365`
  - `Exchange`
  - `DefenderATP`
  - `SharePoint`
  - `OneDrive`
  - `Teams`
- `Exclude` - Specifies which of the sub-reports to exclude. This switch is useful when you're testing or if there are certain items that you don't need in the report. This accepts multiple values. Valid values are:
  - `Microsoft365AssignedLicenses`
  - `Microsoft365ActiveUsers`
  - `Microsoft365ProductActivation`
  - `Microsoft365Groups`
  - `ExchangeMailboxUsageAndProvisioning`
  - `ExchangeClientAppUsage`
  - `ExchangeMailFlow`
  - `ExchangeTop10MailTraffic`
  - `DefenderATPDetections`
  - `SharePointUsageAndStorage`
  - `OneDriveUsageAndStorage`
  - `TeamsUsers`
  - `TeamsUsersActivities`
  - `TeamsDevices`
- `SendEmail` - A switch parameter indicating to send the report via email.
  - `From` - Send email address. This key is required if the `SendEmail` switch is used.
  - `To` - One or more TO recipients. This key is required if the `SendEmail` switch is used.
  - `Cc` - One or more CC recipients. Optional.
  - `Bcc` - One or more BCC recipients. Optional.
- `ShowReport` - This switch indicates whether the report will be opened automatically in the web browser once generated. Use this only when running the report interactively.

## Prerequisite Connection

Before using this module, you must log in to Microsoft Graph and Exchange Online PowerShell.

> The example below pertains to using app-only authentication used for automation that requires an Entra app registration. Replace the values with yours.
>
> You may connect using your own admininstrator account so long as it complies with the [For Manual and Interactive Use](http://127.0.0.1:4000/m365usagereport/#for-manual-and-interactive-use) requirements.
{: .prompt-info }

```powershell
# Define the connection parameter values
$clientId = 'client-id-here'
$tenantId = 'organization.onmicrosoft.com'
$certificateThumbprint = 'certificate-thumbprint-here'

# Connect to Microsoft Graph
Connect-MgGraph -TenantId $tenantId -ClientId $clientId -CertificateThumbprint $certificateThumbprint

# Connect to Exchange Online
Connect-ExchangeOnline -Organization $tenantId -AppId $clientId -CertificateThumbprint $certificateThumbprint -ShowBanner:$false
```

![Shell Connected](/assets/images/m365usagereport/connect_shell.png){: .normal } _Connect to Graph and EXO_

## Usage Examples

### Generate the 30-day usage report of all workloads and send it by email

```powershell
# Import the module
Import-Module PS.M365UsageReport

New-M365UsageReport `
    -ReportPeriod 30 `
    -SendEmail `
    -From 'Microsoft365UsageReports@contoso.com' `
    -To 'ReportAdmin@contoso.com', 'ReportGroup@contoso.com'
```

![Generate the 30-day usage report and send it by email](/assets/images/m365usagereport/example_01.png){: .normal } _Generate the 30-day usage report and send it by email_

### Generate usage reports for selected workloads only

This example shows how to generate the report for selected services or workloads only. For example, SharePoint Online and OneDrive for Business.

The `-ShowReport` switch will automatically open the HTML report in the default browser. This example will not send the report by email.

```powershell
# Import the module
Import-Module PS.M365UsageReport

New-M365UsageReport `
    -ReportPeriod 30 `
    -Scope SharePoint, OneDrive `
    -ShowReport
```

The output will be similar to this:

![Generate usage reports for selected workloads only](/assets/images/m365usagereport/example_02.png){: .normal } _Generate usage reports for selected workloads only_

## Annex

### Register a New Azure AD App

Navigate to the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigate to ***App registrations***. Then, click on **New Registration**.

![New registration](/assets/images/m365usagereport/new_reg.png){: .normal } _New registration_

- In the **Name**, type in ***Microsoft 365 Usage Reports*** (or any name you prefer).
- In the **Supported account types**, select ***Accounts in this organizational directory only***.
- In the **Redirect URI (optional)**, select ***Web*** and type in `http://localhost`.
- Click on **Register**.

![Register an application](/assets/images/m365usagereport/reg_name.png){: .normal } _Register an application_

After the App has been registered, copy the Application (client) ID and Directory (tenant) ID.

![Application details](/assets/images/m365usagereport/reg_name.png){: .normal } _Application details_

### Adding the Required Microsoft Graph API Permissions

Go to **API Permissions** and click on the **Add a Permission** button

![Add a permission](/assets/images/m365usagereport/add_a_permission.png){: .normal } _Add a permission_

In the Request API Permission, select Microsoft Graph API.

![Request permission](/assets/images/m365usagereport/request_api_permission.png){: .normal } _Request permission_

In "**What type of permissions does your application require?",** click on **Application**. From the list of permissions, search for and enable the following permissions.

- `Reports.Read.All`
- `Directory.Read.All`
- `Mail.Send`

Once you’re done selecting the permissions, click on the **Add permissions** button.

![Application permissions](/assets/images/m365usagereport/application_permissions.png){: .normal } _Application permissions_

### Adding the Required Exchange Online API Permissions

Click **Add a permission**
Under "**Request API permissions**," click "**APIs my organization uses**."
Search O***ffice 36 Exchange Online*** and click on the result.