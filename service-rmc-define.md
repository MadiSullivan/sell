---

copyright:

  years: 2018, 2022

lastupdated: "2022-02-11"

keywords: billing service, resource management console, pricing plans

subcollection: sell

---

{{site.data.keyword.attribute-definition-list}}

# Defining your service in the resource management console
{: #step2-define}

The process to onboard and manage services through the resource management console (RMC) is limited to existing services. New services must be onboarded through Partner Center. To onboard a new service, see [Getting set up to sell services](/docs/sell?topic=sell-get-started).
{: important}

The resource management console is a tool that helps guide you through delivering your third-party service to the {{site.data.keyword.Bluemix}} catalog. Now that you're approved to deliver your service, you're ready to use the resource management console to register it, start developing it, and define your pricing plans. 
{: shortdesc}

## Before you begin
{: #rmc-pre-reqs}

1. Ensure that you start working on [Creating your documentation and marketing announcement](/docs/sell?topic=sell-content-tasks#content-tasks).
1. Ensure that you're registered with {{site.data.keyword.Bluemix_notm}}. If not, [register](https://cloud.ibm.com/registration){: external} before proceeding.
1. Ensure that you are in the correct account when you begin working in the resource management console.
1. Prepare your {{site.data.keyword.Bluemix_notm}} service name. You must provide both a service name that is used to identify the service by the {{site.data.keyword.Bluemix_notm}} platform, and a display name that your customers see in the {{site.data.keyword.Bluemix_notm}} catalog.

When you register your offering with resource management console, have your {{site.data.keyword.Bluemix_notm}} service name ready. The service name isn't your display name. Your service name must follow these rules:
- Must be all lowercase
- Can't include spaces but can include hyphens (`-`)
- Must be fewer than 32 characters

   Your service name must include your company name. If your company has more than one offering, your service name must include both company and offering as part of the name. For example, the Compose company has offerings for Redis and Elasticsearch. Sample service names on {{site.data.keyword.Bluemix_notm}} for these offerings would be `compose-redis` and `compose-elasticsearch`. Both of these service names include associated display names that are shown in the {{site.data.keyword.Bluemix_notm}} catalog: *Compose Redis* and *Compose Elasticsearch*. Another company (for example, FastJetMail) can have only the single JetMail offering, in which case the service name must be `fastjetmail`.

## Register your service
{: #register}

To get started, log in and register your service.

1. Log in to [{{site.data.keyword.Bluemix_notm}}](https://cloud.ibm.com){: external}.
   
   It’s critical that you’re in the correct {{site.data.keyword.Bluemix_notm}} account. If you have more than one account, ensure that you switch to the right one.
   {: important} 
   
2. Go to the [resource management console dashboard](https://cloud.ibm.com/onboarding/dashboard){: external}.
3. Click **New Resource** to add your resource.
4. Add your resource name. This value is your unique {{site.data.keyword.Bluemix_notm}} service name that you derived in the previous section.
5. Select **No** for the **Is your broker ready for import?** field. We'll guide you through creating a broker in later steps, and you'll return to the resource management console to import your broker after it's developed and hosted.
6. Complete any additional required values on the Summary page, and click **Save**.

You can save your progress in the resource management console and come back later to add more information. The resource management console is designed to help you manage the lifecycle of your service. It's okay if you don't have all the answers for all the fields right away.
{: tip}

## Enter your service metadata
{: #offering-metadata}

On the Catalog Listing page, provide metadata values that are stored in the {{site.data.keyword.Bluemix_notm}} catalog. Additionally, some of these values need to be exported and stored in your service broker where they're used for provisioning and returned as part of the `catalog (GET)` Response. Use these values to help jump-start the development of a service broker in later steps.

1. From the resource management console, go to **Catalog Listing** > **Listing Page**. The Listing Page defines the metadata that is displayed in your {{site.data.keyword.Bluemix_notm}} service dashboard. Complete all the required values and click **Save**. The resource management console does an initial registration of your service with {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM). A notification that your service was registered with IAM is displayed. You can do more with IAM later.
2. From the Catalog Listing page, click the **Settings** tab.
   1. Specify whether your service allows plan changes. The default is no. If you select **Yes**, you need to extend your Open Service Broker to support plan changes for provisioned instances. If your service supports many plans, and you want users to change plans for an existing provisioned instance, you need to enable the ability for users to update their service instance. For more details, see the `/v2/service_instances/{instance_id} PATCH` endpoint in the [Open Service Broker API v2.12](https://github.com/openservicebrokerapi/servicebroker/blob/v2.12/spec.md#updating-a-service-instance){: external}.
   2. Specify whether your service is bindable. Select **Yes** if your service can be bound to applications in {{site.data.keyword.Bluemix_notm}}. If bindable, it must return API endpoints and credentials to your service consumers. When you develop a bindable service, you must use the [bindable operations in the Open Service Broker API v2.12](https://github.com/openservicebrokerapi/servicebroker/blob/v2.12/spec.md#binding){: external}.
   3. Complete the additional required fields, and click **Save**.
   
The Catalog listing page now has a check mark in the navigation, indicating that you passed the minimum requirements to complete this page. If the page is still marked incomplete, you must reopen the page and check for any incomplete required fields.

Your initial offering letter includes a service documentation URL that was generated by the Onboarding Workbench. You must enter that URL into the **Documentation URL** and **Instructions URL** fields.
{: tip}

## Register with IAM
{: #reg-iam}

IAM is required for all services that are onboarded to {{site.data.keyword.cloud_notm}}. See [What is {{site.data.keyword.cloud_notm}} Identity and Access Management?](/docs/account?topic=account-iamoverview#iamoverview) to learn more about IAM concepts and requirements.

The resource management console generates the following IAM values:
- Service ID (generated and stored)
- API Key (generated not stored - shown once)
- Client ID (generated and stored)
- Client Secret (generated not stored)

You're required to provide:
- Redirect URI (You'll return to the resource management console after you've developed your OSB. You derive the Redirect URI from your hosted service broker's location)

1. From the resource management console, go to the Access Management page.
2. Click **Enable IAM**. The resource management console registers your service with IAM, create your Service ID and Policy, and create an API Key for you. Additionally, it creates an incomplete Client ID and Secret. The Client ID must be updated with your redirect URI after you've it.
3. Click **Status** to see the current state of your IAM enablement.

You need to come back to the IAM page later to provide your `Redirect URI`. You must do some additional development to build an authentication flow to get this value. Later steps help guide you through discerning your Redirect URI value.
{: note}

You’re given your API Key when you **Enable IAM**. It’s critical that you save the API Key. The value isn’t shown again. If you lose your API Key, you can delete the key and create a new one: [Manage service ID API keys](/docs/account?topic=account-serviceidapikeys).
{: tip}

## Develop a pricing plan
{: #pricing-plan}

When you onboard your service to {{site.data.keyword.Bluemix_notm}}, you must define a pricing plan. If you have detailed knowledge about how you want to charge users for your service, you can provide that information in your plan. However, if you haven’t settled on a paid plan, you can start by enabling a free plan, and then come back and set up a paid plan later.

1. From the resource management console, go to the Pricing page.
2. Click **Add plan** to create a new plan entry and click **Edit plan** to edit the plan.
   * **Free plan**: All services can have one Lite plan that is free of charge, allowing users to try out your service. Free plans use *Lite* for the **Display Name** and *lite* for the **Programmatic Name**. Specify **Yes** for **Is this plan free?**, and click **Save**. Your plan is published to the {{site.data.keyword.Bluemix_notm}} catalog.
   * **Subscription plan**: Specify **No** for **Is this plan free?**. Complete the required fields. Leave the default **Pricing metrics** metric. This default metric is provided for you to use to charge users a one-time monthly fee. Click **Save**. Your plan is published to the {{site.data.keyword.Bluemix_notm}} catalog. Save the sample curl command to submit usage.
   * **Metered plan**: Specify **No** for **Is this plan free?**. Complete the required fields. Delete the default **Pricing metrics** subscription metric. Click **Add another metric**, complete the Add pricing metric page, and click **Add metric**. Click **Save**. Your plan is published to the {{site.data.keyword.Bluemix_notm}} catalog. Save the sample curl command to submit usage. For help with selecting the right metrics, see [Metering integration](/docs/sell?topic=sell-meteringintera).

You're required to automate hourly usage submission for all metered plans. For more information, see [Submitting usage for metered plans](/docs/sell?topic=sell-submitusage#submitusage).
{: tip}

## Export your metadata as JSON
{: #export-metadata}

Now that you defined your service within the resource management console, you can download a `catalog.json` file and use it to inform the development of your Open Service Broker. The `catalog.json` file has metadata that must be hosted in your broker. These values define the contract between the broker and the {{site.data.keyword.Bluemix_notm}} platform for the services and plans that the broker supports. All additional catalog metadata that isn’t required for provisioning is stored within the {{site.data.keyword.Bluemix_notm}} catalog. Any updates to catalog display values that are used to render your dashboard like links, icons, and i18n translated metadata, are updated in the resource management console. They aren't housed in your broker. None of the metadata stored in your broker is displayed in the {{site.data.keyword.Bluemix_notm}} console or the {{site.data.keyword.Bluemix_notm}} CLI. The console and CLI return what was set within resource management console and stored in the {{site.data.keyword.Bluemix_notm}} catalog.

1. From the resource management console, go to the Deployments page.
2. Click **Manage**.
3. Click **Download Definitions**.

Save your `catalog.json` file. You’ll use it in to develop your Open Service Broker in the next section.

## Next steps
{: #cis2-next-steps}

Way to go! You defined your service by adding catalog display metadata, registering with IAM, and creating one or more pricing plans. Next, you can take your exported JSON and begin developing a service broker. See [Developing and hosting service brokers](/docs/sell?topic=sell-step3-osb#step3-osb).
