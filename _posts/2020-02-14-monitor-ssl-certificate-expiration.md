---
layout: post
title:  "Monitoring SSL Certificate Expiration using PowerShell"
author: june
categories: [PowerShell]
tags: [ssl,certificate,script,module]
image: assets/images/monitor-ssl.jpg
description: "Use PowerShell to Monitor and Report SSL Certificate Expiration"
featured: true
hidden: true
toc: true
comments: true
---
Have you ever had an SSL certificate get expired and you didn't know about it? Perhaps it was the certificate of a web server or an Exchange server. No matter which, it would be nice to be alerted of expiring certificates, right?

This module, *`LazyExchangeAdmin.ExchangeCertNotify`*, can be used to get a list of server certificates that will expire in specified exact number of days. Additionally, it can also be used to create an HTML output that can be useful for email reports.

## Requirements

* Windows PowerShell 5.1 (sorry, no PowerShell Core)
* Download the module [here](https://github.com/junecastillote/LazyExchangeAdmin.CertX/archive/master.zip), and then export it to your preferred folder.

## How to Use

### Get Details of All Certificates

    Import-Module .\LazyExchangeAdmin.CertX.psd1
    Get-CertXList -ServerList Server1,Server2

The output below shows the certificates retrieved from two servers namely AU-DC01 and AU-FILE01.

![Example01](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/example01.png)

This output supports piping, too. This means you can export the output to CSV like so:

    Get-CertXList -ServerList Server1,Server2 | Export-Csv -NoTypeInformation -Path .\certs.csv

And you should get a CSV file similar to the one below.

![CSV Output](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/csv_output.png)

### Getting Details of Certificates Expiring in a Specific Number of Days

    Import-Module .\LazyExchangeAdmin.CertX.psd1
    Get-CertXList -ServerList Server1,Server2 -ExpiringInDays 14,6

> The `-ExpiringInDays` parameter accepts multiple integer values.

The output below shows that only those certificates that will expire in EXACTLY 6 and 14 days are returned.

![Example02](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/example02.png)

This is useful when you want to use it in a script to send you an alert only when the certificates are about to expire in specific days remaining.

### Converting the Output as HTML Report

    Import-Module .\LazyExchangeAdmin.CertX.psd1

    Get-CertXList -ServerList Server1,Server2 | `
    ConvertTo-CertXHtml | `
    Out-File .\Certs.HTML

![HTML Output](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/html_output.png)

## Helper Script for Monitoring by Email

Copy and save this helper script and name it as `CertX.ps1`.

> Do not forget to edit the variables between the *`## START EDIT HERE`* and *`## STOP EDIT HERE`* lines.

    ## START EDIT HERE

    # [REQUIRED] Where can I find the module?
    $modulePath = "C:\Scripts\CertX\LazyExchangeAdmin.CertX.psd1"

    # [REQUIRED] Where will the HTML report be saved?
    $reportFile = "C:\Scripts\CertX\CertReport.html"

    # [REQUIRED] The servers to be monitored
    $serverList = @('devpc')

    # [OPTIONAL] Expiring in Days
    $expiringInDays = @(7,15,30,60)

    # [OPTIONAL] Excluded these thumbprints from the monitoring
    $exclusionList = @('04F9D994251D33E155AB92593CA0D997D2126AC1')

    # EMAIL PROPERTIES
    $mailProps = @{
        SmtpServer = 'localhost'
        From = 'reports@posh.lab'
        To = 'admin@posh.lab','june@posh.lab'
        Subject = 'Certificate Expiration Report'
    }

    ## END EDIT HERE

    Import-Module $modulePath

    $CertXProps = @{
        ServerList = $serverList
    }

    if ($expiringInDays) {
        $CertXProps.ExpiringInDays += $expiringInDays
        Write-Output ('>> Looking for certificates that will expire in exactly ' + ($expiringInDays -join ",") + ' day(s)')
    }
    if ($exclusionList) {
        $CertXProps.ExclusionList += $exclusionList
        Write-Output ('>> These certificates will be excluded:')
        Write-Output ($exclusionList -join "`n")
    }

    $certXobject = Get-CertXList @CertXProps

    if ($certXobject) {
        Write-Output ('>> Found ' + (@($certXobject).count) + ' certificate(s)')
        $certXobject | ConvertTo-CertXHtml | Out-File $reportFile
        Write-Output '>> Sending email report'
        Send-MailMessage @mailProps -Body (Get-Content $reportFile -raw) -BodyAsHtml
    }
    else {
        Write-Output ('>> Found ' + (@($certXobject).count) + ' certificate(s)')
        Write-Output '>> Email abort'
    }

The screenshot below shows that helper script in action.

![Example](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/mailing.png)

And the report below is what you can expect to be delivered as email.

![Email Report](https://raw.githubusercontent.com/junecastillote/LazyExchangeAdmin.CertX/master/source/static/image/email_report.png)

If you found issues or have any recommendations/suggestions, you may [open an issue here](https://github.com/junecastillote/LazyExchangeAdmin.CertX/issues).
