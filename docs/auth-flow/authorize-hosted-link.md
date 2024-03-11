---
title: "Authorize with Hosted Link"
description: "See how to integrate Hosted Link into your authorization flow"
unlisted: true
---

<head>
  <meta property="og:image" content="/img/link/link-banner.png"/>
</head>

:::tip Codat recommends Link SDK

Instead of Hosted Link, use the [Link SDK](/auth-flow/authorize-embedded-link) to fully embed our flexible, white-labeled auth flow in your application. You will benefit from our extensive experience combined with authorization best practices, providing your users with a native feel of the auth flow.

:::

## Overview

Choose Hosted Link to use an out-of-the-box authorization solution built, provided, and hosted by Codat. To connect your customers' financial software, you can:

- Integrate the Hosted Link auth flow into the user interface of your existing app, or
- Initiate the authorization journey by sharing your Hosted Link URL.

![](/img/link/link-banner.png)

## Integrate the flow into your app

First, create a company to represent your SMB in Codat. We recommend doing that at the time your SMB user signs up within your app. This way, you can track their connection status from day one. 

To create a new company, use the [Create company](/platform-api#/operations/create-company) endpoint and provide a name for the company in the request body. For details on managing and deleting existing companies, review [Manage companies with our API](/using-the-api/managing-companies).

:::tip Use your customer's ID for the company name

For the company `name` parameter, we recommend you pass the ID that you use for the customer in **your** internal system. This makes it easier to identify the Codat company that corresponds to your record of the customer.
:::

From the response to the request, retain the following parameters:

   - `companyId`, because you will need it for directing your customers to Link and managing their connections;
   - `redirect` URL value, because you will use this URL in your app to direct the customer to start their Link journey.

Once your customer finishes the Link flow, they will be redirected back to the URL you have set in the [Link settings](/auth-flow/customize/set-up-redirects). You can also present your customer with a confirmation screen that shows [the integrations they have set up](using-the-api/webhooks/core-rules-types#company-data-connection-status-changed).

Review the parameters in the example response to creating a new company:

```json
{
  // companyId - retain this
  "id": "1126743b-113d-4d72-b14f-36d6742df487",
  "name": "Superapp",
  "platform": "",
  // redirect - use to redirect your customer to Hosted Link
  "redirect": "https://link.codat.io/company/1126743b-113d-4d72-b14f-36d6742df487", 
  "dataConnections": [],
  "created": "2022-05-16T14:55:21.6076495Z"
}
```

To enhance your Hosted Link experience, [set up a webhook](/auth-flow/customize/set-up-webhooks) to monitor the connection status of the newly created company. We recommend that you set up a webhook for when a user authorizes a data connection so that you can action it within your app.

:::note Use the `redirect` Link URL for existing customers

Ensure to direct the existing customers to the `redirect` Link URL that you can retrieve from the company's metadata. If you create a new company and establish a new connection for a customer previously connected, you may be billed for it based on your contract.
:::

#### Manage existing users with active connections

Direct the user to the `redirect` URL that can be retrieved from a company's metadata. This allows them to modify their connections via the Hosted Link UI.

#### Manage existing users with pending connections

When a user creates a data connection, but fails to authorize access to their financial platform, a data connection is created in a [pending status](/core-concepts/connections#data-connection-status) in the respective Codat company.

In this scenario, allow your user to authorize this connection by sending them directly to the third-party authentication screen. Use the `linkUrl` value from the `dataConnections` array in the company's metadata. This will prompt the user to authorize the connection to their financial platform.

## Initiate the journey via URL

### Use the Hosted Link URL

Use the Hosted Link URL if your customer connection is meant to be persistent, and your customer may need to view or update their connection in the future.

If this is a new user:

1. Create a [company via Portal](/configure/portal/companies#add-a-new-company) for your customer.
2. Navigate to the **Companies** page in Portal.
3. Find the company that you created for the customer, and click **Request data** next to the company name.
4. Copy the Link URL from the box that appears and share it with the customer. This will allow the customer to create and authorize a data connection.

If a user already has an integration connection that hasn't been authorized and is not in an active state, click the integration name and copy the URL from the **Connections** section instead of the Link URL. Share this link with the user to allow them to authorize the specific connection.

<img
  src="/img/old/4c41ef0-manage.png"
  alt="Connection URL modal to manage an existing connection"
/>

### Use the Invite company URL

If you want to onboard a large number of new companies without needing to specify company names or references, **AND** your company and your customer do not need to manage, view, or update connections in the future, use the Invite company URL:

1. Sign in to the Codat portal and navigate to the **Companies** page.
2. Click on the `Invite company` button and copy the URL that displays to share it with your customer. Before sharing, ensure that the page title and body text are correctly configured as per [Link customization settings](/auth-flow/customize/customize-link).

:::note Use the Link URL for existing customers

Using the Invite company URL will not allow your customer to view, update or add new connections against the existing Codat company which represents your customer. Therefore, you should use the Link URL to manage connections for existing customers.

:::

## Customize the flow

Our Link settings allow you to configure the authorization process based on your data needs and white-label Link to suit your brand. You can customize these settings in the Codat Portal in Settings > Auth flow > Link.



## 💡 Tips and traps

- Hosted Link is not compatible with iframes. This is against our security policies and will not work. If you want to embed Link inside your website, try our [Link SDK solution](https://docs.codat.io/auth-flow/authorize-embedded-link).
- Some users may be concerned sharing their data at a `codat.io` domain. You should consider pre-warning them that they about to visit a third party website for the authorization process, or try our [Link SDK solution](https://docs.codat.io/auth-flow/authorize-embedded-link).

