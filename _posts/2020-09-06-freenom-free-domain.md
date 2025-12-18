---
layout: post
title:  "How to Get a Free Domain From Freenom"
author: june
categories: [DNS]
tags: [Freenom,DNS,Domain]
image: assets/images/freenom/freenom-post-image.png
imageattribution: Photo by <a href="https://www.pexels.com/@8moments?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels"  target="_blank">Simon Matzinger</a>
description: "Are you looking to get a free domain for temporary or permanent use? Then you should consider Freenom. Let me show you how!"
featured: false
hidden: false
toc: true
comments: true
---

> Note: Freenom was [Sued by Meta, Freenom Halts Domain Registrations](https://krebsonsecurity.com/2023/03/sued-by-meta-freenom-halts-domain-registrations/). Hence, this post is no longer valid.
{: .prompt-info }

Yes, domains can be free. Well, that is if you would not mind using [Top-Level Domains (TLD)](https://www.namecheap.com/domains/what-is-a-tld-definition/) such as .TK, .ML, .CF, .GQ, and .GA. Other than the limited TLD options, there's essentially no difference between a free and paid domain in terms of usability.

## Get a Free Domain from Freenom

Free domains can be registered with *[freenom](https://www.freenom.com/)*. All you have to do is go to their website, check the availability of the domain you want, checkout, and register a new account. Their free domains can be registered for free for 12 months and can be renewed for an unlimited number of times.

## Check Domain Name Availability

First, go to *[freenom.com](https://www.freenom.com/)*. Then type in the name of the domain to check and click on **Check Availability**.

![Check availability](/assets/images/freenom/freenom001.png)
_Checking domain name availability_

When you are presented with the results, select one or more domain names. Then, click on **Checkout**.

![Select domains](/assets/images/freenom/freenom002.png)
_Select domains_

Select the period for how long you need the domain to be registered. The free registration can be up to 12 months. When you've made your selection, click on **Continue**.

![Select the domain registration period](/assets/images/freenom/freenom003.png)
_Select the domain registration period_

## Checkout the Domain and Register for a New Account

>*Note that the procedure below assumes that you do not have a freenom account yet.*

When you reach the **Review & Checkout** page, you have two options to proceed.

- Use an email address to register a new account.
- Use your Google or Facebook account to sign-up for a new account.

It is up to you to choose which registration option to use. In this example, I'm choosing the first option. Enter the email address and click on **Verify My Email Address**.

![Sign up for a new account](/assets/images/freenom/freenom004.png)
_Sign up for a new account_

The next step is to open your mailbox and look for the message sent by freenom. Click on the link in the email you received to continue with the registration.

![Verify email address](/assets/images/freenom/freenom005.png)
_Verify email address_

>*It may take a few minutes to several hours before you receive the verification email from freenom. Be patient.*

The link in the message brings you to the Freenom registration form. Make sure to enter all required information, agree to the *terms and conditions*, and click on **Complete Order**.

![Complete the registration](/assets/images/freenom/freenom006.png)
_Complete the registration_

Then you will get a confirmation about your new domain name order. Click on the button that says, **Click here to go to your Client Area**.

![Order confirmation](/assets/images/freenom/freenom007.png)
_Order confirmation_

When you go to **My Domains** in your freenom client area, you should see that the new domain has been registered.

![New domain is registered](/assets/images/freenom/freenom008.png)
_New domain is registered_

## Confirm That Your Domain Can Be Resolved

To ensure that your domain is resolvable publicly, you could try testing the domain using online tools such as [intoDNS](https://intodns.com/) and [MX Toolbox](https://mxtoolbox.com/DnsLookup.aspx). You can also use command-line tools such as `nslookup` and `Resolve-DNSName`.

In the example below, the new domain will be checked by using *intoDNS*.

First, go to the [intoDNS](https://intodns.com/) website. Then, type in your domain name in the box and click on **Report**. Wait for the DNS report to appear.

![Testing the Freenom Domain](/assets/images/freenom/freenom009.png)
_Testing the Freenom Domain Name Resolution_

## Conclusion

Freenom offers free domain name registration with unlimited free renewal options. Once you've registered a domain, you can manage your DNS records using Freenom's DNS Management tool.

However, you must understand that the type of DNS records that you can create using the built-in DNS management is limited to *A*, *AAAA*, *CNAME*, *LOC*, *MX*, *NAPTR*, *RP*, and *TXT*.

![Freenom DNS Record Types](/assets/images/freenom/freenom010.png)
_Freenom DNS Record Types_

Suppose you require more DNS record types, like the [SRV record](https://www.pair.com/support/kb/what-is-an-srv-record/) used in [Microsoft Teams/Skype for Business](https://docs.microsoft.com/en-us/microsoft-365/admin/get-help-with-domains/create-dns-records-at-any-dns-hosting-provider?view=o365-worldwide#add-srv-records-for-communications-services-teams-skype-for-business). In that case, you may need to consider using [Cloudflare](https://www.cloudflare.com/) to manage your DNS records. Luckily, Cloudflare also offers free DNS records management - but that's an article for another time :)

Thank you for reading!
