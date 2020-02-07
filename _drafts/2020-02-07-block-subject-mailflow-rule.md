---
layout: post
title:  "Office 365 Mail Flow Rule: Block Messages Based on Subject"
date:   2020-02-07 23:10:0 +0800
categories: [Office 365]
author: June Castillote
---
# Office 365 Mail Flow Rule: Block Messages Based on Subject

Email is a vital business enabler in most organizations, and it is a trusted medium of communication in which business or personal information is often shared. This makes email a prime target for attackers and interceptors.

Spam and phishing are the most common types of threats that an Exchange administrator must work on. However, these threats can be tricky, and at times, you may need to resort to using [*mail flow rules*](https://docs.microsoft.com/en-us/exchange/security-and-compliance/mail-flow-rules/mail-flow-rules) to combat those that made it past the perimeter mail checks.

In this post, you will learn how to create a mail flow rule (also known as transport rule) that will block an email based on the subject.

## Prerequisites

The mail flow rule will be created in Exchange Online, which require you to have the following:

- Office 365 Subscription. If you do not have this yet, you can [Sign up for an Office 365 E5 trial subscription](https://www.google.com/url?q=https://aka.ms/e5trial&sa=D&ust=1567614949950000)
- Exchange Admin Role

## Creating the Mail Flow Rule

Mail flow rules can be applied to messages flowing in all directions (inbound, outbound, internal). There are legitimate reasons to target one or all the mail flow depending on your requirements.

Follow the steps below to create a mail flow rule that will block all incoming and outgoing emails based on the subject's keyword. If the mail is intercepted by the rule, the sender will receive an auto-generated response from Exchange Online.

- Login to [https://admin.microsoft.com](https://admin.microsoft.com) using your office 365 admin account

    ![IMAGE_Untitled.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled.png)

- Scroll down to view **Admin Centers** section then select **Exchange**

    ![IMAGE_Untitled%201.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%201.png)

- In **Exchange admin center (EAC)**, Select **Mail Flow** tab then Rules tab

    ![IMAGE_Untitled%202.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%202.png)

- Click the "+**"** symbol and select **Create a new rule**

    ![IMAGE_Untitled%203.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%203.png)

- A new blank rule dialog box will appear like screenshot below

    ![IMAGE_Untitled%204.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%204.png)

- Give your rule an appropriate name which is easy to identify by others. Type “**Block subject of email based on keyword**”

    ![IMAGE_Untitled%205.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%205.png)

- Set the condition "Apply this rule if..." to "The subject or body includes..."

    ![IMAGE_Untitled%206.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%206.png)

- Click the **'Enter words...** then type the keyword **#SYSGEN#** and click the "**+**" symbol. Then, click **OK**.

    ![IMAGE_Untitled%207.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%207.png)

- Set the action of the rule to **"Do the following**...to **"Reject the message with the explanation...** and click the **"Enter text...**

    ![IMAGE_Untitled%208.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%208.png)

- Set the specify rejection reason to "**Blocked by email security policy**" then click **OK**

    ![IMAGE_Untitled%209.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%209.png)

- Set the **Priority** of the mail flow rule to 0.

    ![IMAGE_Untitled%2010.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2010.png)

    Priority indicates the order that the rules are applied to messages. The default priority is based on when the rule is created (older rules have a higher priority than newer rules, and higher priority rules are processed before lower priority rules). 0 will be the top in the priority list.

- Leave the audit option to **Not specified** as we have no intention to audit the number of times this rule will be matched.

    ![IMAGE_Untitled%2011.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2011.png)

- Select **Enforce** in Choose a mode for this rule, so the rule will be applied immediately.

    ![IMAGE_Untitled%2012.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2012.png)

- Select “**Stop processing more rules**” to prevent conflict to other existing mail rules.

    ![IMAGE_Untitled%2013.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2013.png)

- Click **Save**

    ![IMAGE_Untitled%2014.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2014.png)

After mail flow rule is created or modified, it can take up to 30 minutes for the new or updated rule to be applied to messages.

- Refer to the screenshot below for the complete settings of the mail flow rule.

    ![IMAGE_Untitled%2015.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2015.png)

    ![IMAGE_Untitled%2016.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2016.png)

## Testing the Mail Flow Rule

Once you've created the mail flow rule and configured it, you will need to conduct a series of tests to make sure that rule is working as expected. These next examples are the bare minimum that you need to do for testing and verification.

### Testing External to Internal Messages (or Vice Versa)

Send an email using an external sender to an internal mailbox and make sure there is ‘#SYSGEN#’’ word in the subject line.

![IMAGE_Untitled%2017.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2017.png)

![IMAGE_Untitled%2018.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2018.png)

Auto-generated response from Exchange Online Server

### Testing Internal to Internal Messages

Send an email using a sender from inside your organization to to another and make sure there is ‘#SYSGEN#’’ word in the subject line.

![IMAGE_Untitled%2019.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2019.png)

![IMAGE_Untitled%2020.png](https://raw.githubusercontent.com/junecastillote/junecastillote.github.io/master/static/img/_posts/block-subject-mailflow-rule/Untitled%2020.png)

Auto-generated response from Exchange Online Server

# Summary

In this article, you’ve learned how to create and configure a mail flow rule in an exchange online admin center to protect your users from a deemed malicious email based on the subject.

Mail flow rule offers a lot of conditions, there may be times  that you need to add an exception or adjust more setting to meet the business requirement but just make sure not to mess up as it can lead to a service interruption on email service

# Reference

[Mail flow rules (transport rules) in Exchange Online](https://docs.microsoft.com/en-us/exchange/security-and-compliance/mail-flow-rules/mail-flow-rules#how-mail-flow-rules-are-applied-to-messages)
