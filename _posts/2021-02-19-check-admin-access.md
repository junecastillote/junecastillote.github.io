---
layout: post
title:  "How To Check If You Have An Administrator Account On A Computer"
author: june
categories: [How-To,Windows,macOS]
tags: [macOS,Windows 10,Administrator]
image: assets/images/check-admin-access/check-admin-access-post-image.jpg
imageattribution: Photo by <a href="https://unsplash.com/@tfrants?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Tyler Franta</a>
description: "Do you know if your have admin access on your computer? Let's find out!"
featured: false
hidden: false
toc: false
comments: true
---

<br>
Unless you installed and set up your computer's operating system, there's a chance that your user account doesn't have local administrator access. Your day-to-day tasks may not need admin access until you install software or make system-wide changes.

But how do you find out whether you have local administrator access to your computer? Well, that's what you'll learn here.

- [Windows 10](#windows-10)
  - [Method 1: Windows Settings (Accounts)](#method-1-windows-settings-accounts)
  - [Method 2: Control Panel (User Accounts)](#method-2-control-panel-user-accounts)
  - [Method 3: Command Prompt](#method-3-command-prompt)
  - [Method 4: PowerShell](#method-4-powershell)
- [Linux](#linux)
- [macOS](#macos)
- [Conclusion](#conclusion)

## Windows 10

There are several ways to check if a user has local admin access in Windows 10. In this section, you'll learn four ways to do just that.

### Method 1: Windows Settings (Accounts)

If you're working in Windows 10, your instinct would be to click your way to your goal. That's what Windows has intended anyway, right? Follow these steps.

1. The first step is to open the **Your info** section in Windows Settings. To do that, type "Your info" in the search box.
2. In the search results, click **Open**.

   ![Opening "Your Info"](/assets/images/check-admin-access/open-your-info.jpg)

3. Then, under your account name, you'll see the word **Administrator** if you're an administrator.

   ![Check "administrator" under your name](/assets/images/check-admin-access/administrator-under-name.jpg)

### Method 2: Control Panel (User Accounts)

Another way to confirm whether you are an administrator is by checking your group membership using the **Control Panel User Accounts** list.

1. Open the **Run** dialog box and type in `control userpasswods2`. Then, press Enter or click **OK**.

   ![Open the Run dialog](/assets/images/check-admin-access/open-run-box.jpg)

2. In the **User Accounts** window, look for your user account. Then, check whether your account is a member of the *Administrator* group.

   ![Open the Run dialog](/assets/images/check-admin-access/user-accounts-list.jpg)

### Method 3: Command Prompt

The following method is another quick way to determine whether you’re an administrator using the command prompt.

1. First, open the command prompt.

   ![Open Command Prompt](/assets/images/check-admin-access/open-cmd.jpg)

2. Then, run this command below. Make sure to replace [your username] with your username.

    ```PowerShell
    net user [your username]
    ```

    In the result, look for **Local Group Memberships** and confirm that it includes **Administrators*.

   ![net user](/assets/images/check-admin-access/net-user.jpg)

3. An alternative is to list the members of the *Administrators* group instead. While still in the command prompt, run this command

    ```PowerShell
    net localgroup Administrators
    ```

    Then, see if your username is on the list.

   ![net localgroup](/assets/images/check-admin-access/net-localgroup.jpg)

### Method 4: PowerShell

Lastly, using the Get-LocalGroupMember PowerShell cmdlet, you can list all members of the Administrators group.

1. First, open **Windows PowerShell**.

   ![Open Windows PowerShell](/assets/images/check-admin-access/open-powershell.jpg)

2. Then, run this command below.

   ```PowerShell
   Get-LocalGroupMember -Group Administrators
   ```

   In the results, look for your username to confirm whether you are a member of the Administrators group.

   ![List Administrators](/assets/images/check-admin-access/Get-LocalGroupMember1.jpg)

   Another way is to use the same command but explicitly target your user account. Use the command below in PowerShell.

   ```PowerShell
   Get-LocalGroupMember -Group Administrators -Member ($env:USERNAME)
   ```

   After running the command, you should see whether you are a member of the Administrators group.

   ![List Administrators](/assets/images/check-admin-access/Get-LocalGroupMember2.jpg)

## Linux

In Linux, the way to check if you are an Administrator is relatively standard in all distributions. Technically, administrators in Linux are called *sudoers*, and they are members of the **sudo** group.

In the BASH terminal, type this command below.

```BASH
sudo whoami
```

Enter your password when prompted. If the result shows **root**, it means your user account is a **sudoer** or an administrator.

![Sudo Check](/assets/images/check-admin-access/sudo1.jpg)

Conversely, if your account is not a sudoer, you'll get a similar message, as shown below.

![Sudo Check](/assets/images/check-admin-access/sudo2.jpg)

## macOS

Finally, if you have a Mac, you can check whether your account is an administrator in three steps.

1. Click on **System Preferences** in the Dock.
2. Click on **Users & Groups**.

    ![Open Users and Groups](/assets/images/check-admin-access/mac1.jpg)

3. Look for your account name on the left-hand side. If you see the word **Admin** under your username, that confirms that you are an Administrator.

   ![Open Users and Groups](/assets/images/check-admin-access/mac2.jpg)

## Conclusion

That's it! Now you know the different ways to check if you're an administrator. Whether you're using Windows, Linux, or macOS, there's an appropriate method for you.
