<h1>Quickbooks Online Connector for BTCPay Server</h1>

Copyright (C) 2018 Jeff Vandrew Jr

<h2>Introduction</h2>

In addition to its bookkeeping capabilities, Quickbooks Online (QBO) is currently the most popular small business online invoicing solution. When customers receive an online invoice from a business using QBO via email, they currently have the option to pay electronically using credit card or ACH (via integrated Intuit Payments) or Bitcoin (via Intuit PayByCoin, which unfortunately only supports Bitpay and Coinbase). This software extends Quickbooks Online to take payments through BTCPay server. It is self-hosted and does not rely on any third party.

When this plugin is installed, customers choosing to pay a QBO invoice using BTCPay automatically have a BTCPay invoice generated with the customer's data pre-filled, and payments to the BTCPay invoice are autmatically recorded in QBO. Before generating the invoice, the software verifies that the email and invoice number match to prevent against customer typos.

<h2>Notes</h2>

All payments made through BTCPay will be recorded in QBO in an "Other Current Asset" account called "Bitcoin." They are recorded at USD value as of the date the invoice was paid. This is not a bug; it is intentional behavior. The USD value on the payment date is the amount of taxable income recognized as well as the tax basis for a future sale of BTC under US Tax law, so the BTC is recorded in QBO accordingly. The information herein is educational only and is not tax advice; consult your tax professional.

Payments will not record in QBO until the invoice status in BTCPay is "confirmed." Payments are considered "confirmed" based on your BTCPay settings. The default is one on-chain confirmation.

<h2>Setup</h2>

<h3>Activation</h3>

Instructions assume that you are using the Dockerized BTCPay Server, which would be the case if you originally used the one-click install through LunaNode.

You only need to activate the plugin once, as going forward after activation the plugin should update automatically when you update BTCPay.

To activate, log into your LunaNode or other VPS via SSH and run the commands below. Don't forget the trailing hyphen in the first command, as it's important.

```bash
# Log in as root and load environment variables of BTCPayServer
sudo su -

#go into the BTCPay-Docker directory
cd btcpay-docker

# Add this plugin docker fragment
export BTCPAYGEN_ADDITIONAL_FRAGMENTS="$BTCPAYGEN_ADDITIONAL_FRAGMENTS;opt-add-btcqbo"
# Save
. btcpay-setup.sh -i
```

<h3>Post-Activation</h3>

After activation, you can access the plugin directly from within BTCPay under Server Settings/Services, or alternatively at https://btcpay.example.com/btcqbo, replacing btcpay.example.com with the domain where your BTCPay instance is hosted. On the plugin welcome screen, there will be buttons for:

* Entering Intuit API Keys
* Syncing the plugin to QBO
* Syncing the plugin to BTCPay
* Setting up email receipts

<h3>Entering Intuit API Keys</h3>

You need API keys from Intuit to sync to Quickbooks Online.

1. Head to https://developer.intuit.com and log in using the same login you normally use for Quickbooks Online. Your account will then be granted developer privileges.

2. After logging in, click on "My Apps" and create a new app. Call the app "BTCQBO".

3. After the "app" is created, click "Keys". There will be sandbox and production keys. To obtain the production keys, Intuit will require you to fully fill out your developer profile. Intuit will also require links to your privacy policy and EULA; these are largely irrelevant since your own business will the only "user" of your app. If you don't have links to a EULA and privacy policy, you may choose to use these links https://raw.githubusercontent.com/JeffVandrewJr/btcqbo/master/privacy-sample and https://raw.githubusercontent.com/JeffVandrewJr/btcqbo/master/eula-sample. These are provided for educational purposes, and consult with your own attorney if you have questions. 

4. On the Intuit Developer site, underneath your Intuit "production" keys, add "https://btcpay.example.com/btcqbo/qbologged" as a redirect URI, replacing btcpay.example.com with the domain where your BTCPay instance is hosted. Ensure you're doing this in the "production" (not sandbox) area of the page.

5. From the plugin welcome screen, click the button to enter your Intuit API Keys. Enter your Quickbooks Client ID and Quickbooks Client Secret obtained from the Intuit Developer site above. Be sure to use the "Production" rather than "Sandbox" keys (unless you are in fact running on a sandbox test company).

<h3>Syncing QBO</h3>

After entering your Intuit API keys into the plugin, you'll be automatically redirected to sync the plugin to QBO. (If you need to resync later without updating your API keys, from the plugin welcome screen, you can hit the button to sync QBO.) Follow the on-screen instructions to sync to Inuit; you'll need your QBO username and password. You can repeat this step at any time in the future if you become unsynced from Intuit for any reason.

<h3>Syncing BTCPay</h3>

From the plugin welcome screen, hit the button to sync BTCPay. Click on the link provided to obtain a pairing code from your BTCPay Server instance. Then enter the pairing code in the plugin, and submit.

<h3>Email Setup</h3>

Versions 0.1.20 and higher of the plugin allow automatic emailing of customer receipts. To set this up, click the email setup button on the plugin welcome screen. You'll need to enter your SMTP server settings. If you enter a test email recipient, it will test the connection and send a test email.

If you don't know what an SMTP server is, that's OK! Your email provider almost definitely provides SMTP access for free. If it doesn't, you can always sign up for a free account with a provider that does (Gmail, Yahoo, Outlook.com, etc).

Googling your email provider and "SMTP" will likely show you the settings. For example G-Suite (and Gmail) use smtp.gmail.com as host, and use port 587. With Google (and most providers), your email is your username. Your password is your normal password, unless you use Two Factor Authentication. If you use 2FA, you'll need to generate an application-specific password (example instructions for Google are [here](https://support.google.com/mail/answer/185833?hl=en)).

Your "send from" address is the address you want receipts to be sent from, and may be the same as your username. "Business Name" is your business name as you want it to appear on receipts.

<h3>Creating Public Facing Payment Portal</h3>

You will need a page on your business' website to which your Quickbooks invoices can link. You'll also need to edit your Quickbooks templates so that customers know they can pay in Bitcoin! This section will show you how to do both of those things.

These instructions assume your business' normal public facing site is a Wordpress site. 

1. In Quickbooks Online, edit your outgoing email template for invoicing (Gear Icon --> Account & Settings --> Sales --> Messages) like this:
```
Attached is your invoice [Invoice No.]. 

If you'd like to pay in Bitcoin, click: https://example.com/pay

If you'd like to pay in US Dollars, click the "Review & Pay" button below, where you can view the invoice and pay electronically.

Thanks!
```
You'll need to change the "example.com/pay" to the URL you're using in Step 3 below. Leave `[Invoice No.]` alone, as that is a Quickbooks placeholder that will auto-insert the appropriate data when the email is sent. Quickbooks automatically generates the "Review & Pay" button for fiat payments.

2. In addition to editing the email template, also edit your Quickbooks Online invoice template. To get there, in Quickbooks Online, click the gear icon, then Custom Form Styles, then hit "Edit" next your current form style. Then click the block that says "Content". The sample invoice will then become clickable. Click on the bottom content block (where it says "Total Due"). A "Message to Customer" field will then appear. In that field, type:
```
Head to https://yourdomain.com/pay to pay via Bitcoin.
```
Set the font size, and then don't forget to hit "Done" to save your changes in Quickbooks Online.

3. Log into your Wordpress site and create a new page. Title the new page "Make a Bitcoin Payment". Set the URL to match the one you chose in Step 1 (ex: yourdomain.com/pay).

4. Paste the code below into the body:
```
<form method="POST" action="https://btcpay.example.com/btcqbo/verify">
USD Amount:
  <input type="text" name="amount" />
Email Address:
  <input type="text" name="email" />
Invoice Number:
  <input type="text" name="orderId" />
  <input type="hidden" name="notificationUrl" value="https://btcpay.example.com/btcqbo/api/v1/payment" />
  <input type="hidden" name="redirectUrl" value="https://example.com" />
  <button type="submit">Pay now</button>
</form>
```
If you're using the "new" Wordpress 5.0, you'll need to first click the three dots at the top left of the content block and select "Edit as HTML." In the pasted code, change `btcpay.example.com` to the domain of your BTCPay host (but leave all portions of the URL following the ".com" the same). Also set the redirect URL of your choice. Save the page in Wordpress; due to the magic of CSS it should automatically be styled to match your site.

<h4>Troubleshooting (only for technical users!)</h4>

If you are a more technical user, there are some advanced troubleshooting and testing tools explained in the advanced_troubleshooting.md file.
