<h1>Quickbooks Online Connector for BTCPay Server</h1>

Copyright (C) 2018-2019 Jeff Vandrew Jr

Note that at the current time, this plugin can only process on-chain payments, not lightning payments. This is a limitation imposed by the way BTCPay handles payment notifications for lightning payments. [See this GitHub issue for details.](https://github.com/btcpayserver/btcpayserver/issues/564). If the issue is resolved in BTCPay, I will update the plugin to properly process lightning payments.

This plugin for BTCPay Server has two modes of operation:

1. **Invoicing Mode**: If you use Quickbooks Online to send invoices to your customers and want your customers to be able to pay invoices sent from Quickbooks Online in Bitcoin, [click here](https://github.com/JeffVandrewJr/btcqbo/blob/master/invoice-mode.md).

2. **Deposit Mode**: If you do not send customers invoices through Quickbooks Online, but simply want your payments received through BTCPay Server to automatically post to Quickbooks, this mode would be for you. This would be common for online retailers and other businesses receiving payments through e-commerce solutions. [Click here](https://github.com/JeffVandrewJr/btcqbo/blob/master/deposit-mode.md) for setup.
