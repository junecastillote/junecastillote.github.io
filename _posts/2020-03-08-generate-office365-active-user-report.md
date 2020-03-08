---
layout: post
title:  "How to Generate an Office 365 Active User Report using PowerShell and Microsoft Graph REST API"
date:   2020-03-08 19:59:00 +0800
categories: [AzureAD]
author: Mark Daniel Dimalanta
---

There are a lot of reports that can be extracted in Microsoft 365 that helps the administrator in managing the tenant, one of these reports is **Active Users** **report**. This report includes information such as the account’s last activity date on each office 365 services, license information about the services and when the license was assigned. This information can be used in license management, instead of buying a new license - the administrator can identify accounts that are not using the service for the x number of days and allocate the associated license to other or newly created accounts.

You can manually generate the report using [Microsoft 365 Admin Center](https://docs.microsoft.com/en-us/microsoft-365/admin/activity-reports/active-users?view=o365-worldwide) under Reports > usage section. But this is a quite tedious task when you are managing multiple tenants. Fortunately, using PowerShell and Microsoft Graph REST API you can build a script that automatically generates the report.

In this article, you will learn how to generate the active users report in a CSV file using PowerShell and Microsoft Graph REST API.

## Requirements

This article will be a walkthrough. If you intend to follow along, make sure you have the following prerequisites set up ahead of time.

- An Office 365 Tenant. [Request for a trial](https://go.microsoft.com/fwlink/p/?LinkID=698279&culture=en-US&country=US) if you do not have it yet.
- A script editor, like *[Notepad++](https://notepad-plus-plus.org/download/)* or an Integrated Scripting Environment (ISE) like *[Windows PowerShell ISE](https://docs.microsoft.com/en-us/powershell/scripting/components/ise/introducing-the-windows-powershell-ise?view=powershell-5.1)* and *[Visual Studio Code](https://code.visualstudio.com/download)*.
- Windows PowerShell 5.1 or PowerShell Core 6+.
- A registered app in Azure AD with proper permissions. Please refer to *[Register a new application using the Azure portal](https://docs.microsoft.com/en-us/graph/auth-register-app-v2#register-a-new-application-using-the-azure-portal)* if you don't know how to do that.
- The registered app must have the following settings.
    - API to use - *Microsoft Graph API*
    - API permissions to add - *Reports.Read.All*
    - API permission type to select - *Application Permission*
- The registered app must be [*granted admin consent](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/grant-admin-consent#grant-admin-consent-in-app-registrations).*
- The *Application ID, Directory ID* and the *Client Secret* of the registered app

To see the reports via Microsoft admin 365 portal, you must be a global administrator, global reader or reports reader in Microsoft 365 or an Exchange, SharePoint, or Skype for Business administrator. To learn more, see About [admin roles](https://docs.microsoft.com/en-us/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide) and [Assign admin roles](https://docs.microsoft.com/en-us/microsoft-365/admin/add-users/assign-admin-roles?view=o365-worldwide)

## Knowing the API Basics

To authorize the APIs to read Office 365 usage reports, you must take note of the key information that comprises a request.

- An Access Token. The next section will explain how to generate an access token.
- The different request URLs to get the active user details report:

        /reports/getOffice365ActiveUserDetail(period='{period_value}')
        /reports/getOffice365ActiveUserDetail(date='{date_value}')

- Function parameters

    ***period***- A string type of variable that specifies the length of time over which the report is aggregated. The supported values for {period_value} are: D7, D30, D90, and D180. These values follow the format Dn where n represents the number of days over which the report is aggregated.

    ***date*** - A date type of variable that specifies the date for which you would like to view the users who performed any activity. `{date_value}` must have a format of `YYYY-MM-DD`. As this report is only available for the past 30 days, `{date_value}` should be a date from that range.

For example, to get the list of active users detail for 7 days the request URL would be:

    https://graph.microsoft.com/v1.0/reports/getOffice365ActiveUserDetail(period='D7')

## Acquiring Access Tokens

Once you've satisfied all the requirements, the next step is to acquire the access token that will be used to authorize the requests.

 Copy and paste the code below into your script editor. Then, change the value of the `$TenantID`, `$ClientID` and `$ClientSecret` variables with the ones from your registered app. If you don't have this information, refer to this *[link](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)* to find out how and where to get them.

Once you've updated the variables, paste the code into your PowerShell session to run it. The code requests an access token and stores the returned value to the `$token` variable.

    # CHANGE THESE VALUES
    $TenantID = 'TENANT-ID' #The Directory ID from Azure AD
    $ClientID = 'CLIENT-ID' #The Application ID of the registered app
    $ClientSecret = 'CLIENT-SECRET' #The secret key of the registered app
    # ------------------------------------------------------

    # DO NOT CHANGE THESE
    $body = @{grant_type="client_credentials";scope="https://graph.microsoft.com/.default";client_id=$ClientID;client_secret=$ClientSecret}
    $oauth = Invoke-RestMethod -Method Post -Uri https://login.microsoftonline.com/$tenantID/oauth2/v2.0/token -Body $body
    $token = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    # ------------------------------------------------------


The screenshot below shows the contents of the token would look like.

![Contents of the access token](/assets/generate-office365-active-user-report/access-token.png)

Contents of the access token

*Tokens are only valid for 60 minutes. Once it expires, a new access token must be requested.*

## Getting Active Users Report

The Active Users report can be viewed for trends over the last 7 days, 30 days, 90 days, or 180 days. However, data won't exist for all reporting periods right away. The reports become available within 48 hours.

The code snippet below will generate a report for 30 days period and export it to a CSV file. Copy the code below and paste it into your PowerShell session.

    $graphApiUri = "https://graph.microsoft.com/v1.0/reports/getOffice365ActiveUserDetail(period='D30')"
    $Reports = Invoke-RestMethod -Method Get -Uri $graphApiUri -Headers $token | ConvertFrom-Csv
    $Reports | Export-Csv .\Report.CSV -NoTypeInformation

The first command stores the request URL with the period of *D30* in $graphApiUri variable.

The second command uses the **Invoke-RestMethod** cmdlet along with the value of $graphApiUri and $token variables to access the data in office 365. It also uses **ConvertFrom-Csv** cmdlet to format the data and store it in the $Reports variable.

The third command uses **Export-Csv** cmdlet to export the data from the $Reports variable to a CSV file.

## Previewing the report

You can now open the CSV file generated by the API script, notice that the refresh date column is 2 days behind from the current date. As mentioned previously, data won't exist for all reporting periods right away, reports become available within 48 hours. The blank value means no data was captured for the account or the user did not use or log in to the services.

![Reports generated by the API script](/assets/generate-office365-active-user-report/csv-report.png)
<!-- ![Reports generated by the API script]({{site.baseurl}}/img/posts/generate-office365-active-user-report/csv-report.png) -->

Reports generated by the API script | Date of extraction: 28FEB2020

## Conclusion

In this article, you've learned how to generate the **Active Users' Detail Report** using PowerShell and Microsoft Graph API.  You've also learned how to register the application with the proper permission and generate an access token that is used to authorize the requests against the API.

Automating your reports via PowerShell and API is a great way to effectively and efficiently manage multiple tenants without having the need to manually log-in. Aside from what you learned today, there are a lot more you can do with Microsoft Graph API, your journey has just begun – good luck and thank you for reading!

## References

- [Microsoft 365 Reports in the admin center - Active Users](https://docs.microsoft.com/en-us/microsoft-365/admin/activity-reports/active-users?view=o365-worldwide)
- [reportRoot: getOffice365ActiveUserDetail](https://docs.microsoft.com/en-us/graph/api/reportroot-getoffice365activeuserdetail?view=graph-rest-1.0&tabs=http)