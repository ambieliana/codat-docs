---
title: "Build your own auth flow"
sidebar_label: Overview
description: "Create your own journey to connect your customers' financial platforms"
unlisted: true
---

:::tip Codat recommends Link SDK

Instead of building your own solution, Codat recommends using the [Link SDK](/auth-flow/authorize-embedded-link) to fully embed our flexible, white-labeled auth flow component in your application. Benefit from our extensive experience combined with authorization best practices, providing your users with a native feel of the auth flow and an **89%** conversion rate.

:::

If your business scenario and circumstances prevent you from using our Link SDK, you can use the endpoints that allow you to build the journey for your business customers to connect their financial platforms.

:::info Indicative demo

Curious where Codat might fit in to a digital lending onboarding experience? Experience the flow with Copay - a fictional digital lender that gathers financial data to make a credit decision.

[See the demo](https://codat-dev-link-demo.azurewebsites.net/home) | [See the code](https://github.com/codatio/demo-auth-flow)
:::

## 1. Create a Codat company when a user signs up for your app

1. In order to establish a connection to your customer's financial platform(s), you first need to create a Codat company for them. We recommend you create a company at the same time as your customer signs up within your app. That will allow you to track their connection status from day one. To create a company, follow the steps in [Create a Codat company](/using-the-api/managing-companies#create-a-codat-company).

:::caution Use your merchant ID for the company name

We recommend that you populate the name value with the ID that you use for the merchant in **your** internal system so that it’s easier to identify the Codat company that corresponds to your record of the merchant.
:::

2. From the response to step 1, retain the company ID (hereafter referred to as `companyId`) (see an example below). It is crucial that you retain this value as you will need it for directing your customers to Link and managing their connections.
3. _(Optional)_ [Set up a webhook](/auth-flow/customize/set-up-webhooks) to monitor the connection status of the newly created company.

## 2. Display a list of integrations for your users to select, including the integration name and logo

1. Retrieve a list of all integrations available to connect and display them in your UI:

    ```http
    GET /integrations",
    ```

    Use a query filter in the following format:

    ```http
    ?query=sourceType=accounting&&enabled=true
    ```

    In the query above, the `enabled` parameter allows you to filter the integrations based on whether or not they have been enabled via the Codat portal. The `sourceType` parameter allows you to filter the integrations by the data type: `accounting`, `banking` or `commerce`.

    Note that you need to URL encode the query:

    ```http
          "code": "//Non-encoded query:
          ?query=sourceType=accounting&&enabled=true

          //Encoded query:
          ?query=sourceType%3DAccounting%26%26enabled%3Dtrue",
    ```

More information about querying can be found in [Querying](/using-the-api/querying). If you are using the API reference, you don't need to encode the query, it happens automatically.

2. Retrieve branded assets, including logos and buttons, for the selected integrations:

```http
GET /integrations/{platformKey}/branding",
```

:::caution Cache branding assets
To ensure this page is performative for your users, we recommend caching the branding assets information rather than calling the branding endpoint each time a user visits the integration selection page.
:::

:::info Use the branded assets provided by Codat

We recommend using the assets provided by Codat as they meet the requirements of the supported integrations. For example, Intuit integrations (QuickBooks Online and QuickBooks Desktop) require the specific use of QuickBooks branded buttons, including specific hover states.
:::

## 3. Direct your user to enter their 3rd party credentials to authorize a connection with their selected platform

1. Create a data connection for the integration selected by your customer:

```http

POST /companies/{companyId}/connections

Request body:

"platformKey"",
```

The `platformKey` is the unique key Codat uses instead of financial platform names to remove the dependence on a platform's display name. View the list of our [accounting](/integrations/accounting/overview#platform-keys), [commerce](/integrations/commerce/overview#platform-keys), and [banking](/integrations/banking/overview#platform-keys) platform keys, or retrieve them using [our API](/platform-api#/operations/list-integrations).

2. Direct your user to the `linkUrl` found in the nested `dataConnection` object for the specified integration, returned in the response. It will prompt the user to enter their credentials for the 3rd-party platform, authorizing the connection and activating it.

:::info Platform-specific pages

For some integrations, the authorization flow may include additional setup instructions or steps. For example:

- Sage Intacct and NetSuite require complex user permissions
- Microsoft Dynamics 365 Business Central requires your users to install a package on their machine
- Xero requires your customer to confirm the company they want to link after authorizing access to their organization
:::

3. Once the user has successfully authenticated in their platform and thus authorized a data connection, redirect them back to your app. Ensure you handle all possible redirect status codes and error messages so that your users understand when something has gone wrong. Read [Redirect URLs](/auth-flow/customize/set-up-redirects) to learn more about how redirect URLs are used with Codat's products.

If you don't set a redirect URL, the user will be presented with a pre-built success page.

If a redirect URL is not set, then pre-built UI will be presented to your user. This will be the success and overview page of Link - our pre-built white-labelled UI.

:::caution Rules on number of connections to different integration types

A company may link a single source of accounting data but multiple sources of banking or commerce data. Any combination of accounting, banking, and commerce connections is allowed. For more on data connections and connection statuses see [Data connections](/core-concepts/connections).
:::

## 4. Confirm successful authorization and data synchronization

1. Once the connection is complete (for guidance on how to monitor the connection, read [Set up webhooks](/auth-flow/customize/set-up-webhooks)), mark the connection as authorized and confirm to your user the successful authorization of the connection.
2. Monitor the synchronization of data (also available in <a href="/platform-api#/operations/get-company-data-status">our API</a>):

```http
GET https://api.codat.io/companies/{companyId}/dataStatus",
```

Once the initial synchronization of data is complete, you can inform the user accordingly and continue the flow of your app.

## 5. Allow your users to manage their ongoing connection(s)

Have the following values at hand:

- The company `id` of the Codat company that represents the user, hereafter referred to as `companyId`
- The `id` of the connection you want to modify, hereafter referred to as `connectionId`

To get these values:

```
GET /companies

//Example response:
{
        //Company ID
        "id": "40ef18ac-acf0-4ea1-8667-05398c0b75fa",
      //End of company ID
      "name": "Superapp",
      "platform": "",
      "redirect": "https://link.codat.io/company/40ef18ac-acf0-4ea1-8667-05398c0b75fa",
      "dataConnections": [],
      "created": "2022-05-16T15:51:03Z"
    },
    {
      //Platform connection ID
      "id": "1126743b-113d-4d72-b14f-36d6742df487",
      //End of platform connection ID
      "name": "Superapp",
      "platform": "Xero",
      "redirect": "https://link.codat.io/company/1126743b-113d-4d72-b14f-36d6742df487",
      "lastSync": "2022-05-16T15:52:15.6455941Z",
      "dataConnections": [
        {
          "id": "f42c2cbe-dfab-4b13-a16f-51729c75bd2e",
          "integrationId": "0f20c943-12d0-4800-9f6c-d218f62d494c",
          "sourceId": "8a156a5a-39cb-4f9d-856e-76ef9b9a9607",
          "platformName": "Xero",
          "linkUrl": "https://link-api.codat.io/companies/1126743b-113d-4d72-b14f-36d6742df487/connections/f42c2cbe-dfab-4b13-a16f-51729c75bd2e/start",
          "status": "Linked",
          "lastSync": "2022-05-16T15:52:15.6455938Z",
          "created": "2022-05-16T15:00:00Z",
          "sourceType": "Accounting"
        }
      ],
      "created": "2022-05-16T14:55:22Z"
    },
```

#### Allow users to view existing connections for their company

```http

GET /companies/{companyId}/connections",
```

This endpoint is also available in <a href="/platform-api#/operations/list-connections">Common API</a>.

In the request above, the `companyId` is the `companyId` that was returned to you when you created a Codat company for the user. It can also be found in the company's metadata.

#### Allow users to revoke access to their platform

Allow your users to prevent further synchronizations with a connection, whilst still allowing you to access data that has already been pulled or pushed:

```http
POST /companies/{companyId}/connections

Request body:

"platformKey"
```

This endpoint is also available in <a href="platform-api#/operations/create-connection">Common API</a>.

#### Allow users to delete a connection

To delete a connection entirely, preventing both further synchronizations and the ability to make any data pulls or pushes:

```http
DELETE /companies/{companyId}/connections/{connectionId}",
```

### Bonus: Show that your authorization flow is powered by Codat

To [boost your customers' trust](/auth-flow/optimize/privacy#show-that-your-authorization-flow-is-powered-by-codat), you can embed our "Powered by Codat logo" into your application.

You can [download the logo](https://static.codat.io/public/branding/powered-by-codat.svg) or link it from our content delivery network:

<img
  src="https://static.codat.io/public/branding/powered-by-codat.svg"
  alt="Powered by Codat"
/>

## Best practices

## Best Practices

If an end user has linked before, use the relevant existing company rather than creating a new company (even if they have previously deauthorized).

Where possible you should be using webhooks to be informed of when to fetch data, rather than polling our API for dataset status updates.
This will allow you to fetch fresh data as soon as it is available as well as reduce the amount of calls being made to our API.

We (currently) only support data access permissions, not data usage permissions.
This means that the user can consent to us accessing their data as a whole, not which parts or what is done with it.
If you want to manage how the data is used then they will need to manage the permissioning in their system.

Consent is done via OAuth2 and it means until you revoke permission we will be able to access all of the end users data on an on-going basis.

This data is also stored forever (until revoked) in our central data database. This means that it is always available to be accessed via our API and we don’t need to keep going to the accounting platform to get it (and thus not hitting rate limits).

### What do I do when my customer user doesn't have access to the sign in credentials for their accounting/banking/commerce platform?

Often your customer or user doesn't themselves have the sign in credentials to the platforms you need to access. For example, perhaps their accountant is the only stakeholder in their business that actually goes into their accounting platform.

If you're using our Hosted Link solution, your customer can just forward that stakeholder the hosted Link URL. However, for our Link SDK or a custom built auth flow, your authorization flow is likely only accessible when logged in, which means sharing around password and logins - not ideal!

Just because you're not using Link as your primary auth flow, it doesn't mean you can't benefit from it.

Why not try:

1. Making your auth flow an optional part of onboarding
2. Presenting your customers with a CTA inviting them to auth, but also giving them an alternative like 'Can't sign in to your {accounting/banking/commerce} platform?'
3. If a customer clicks this option, you could:
   1. Give them the Hosted Link URL to share themselves: `<https://link.codat.io/companies/{companyId}`>
   2. Use a `mailto:` link to make it easier. E.g.:

```html
<a href="mailto:https://link.codat.io/companies/{companyId}">Invite someone else</a>
```

You can even prefill the subject line and email body as you see fit.

It's important that the request to authorize comes from your customer rather than you to ensure that the need is communicated and trusted.