---

copyright:
  years: 2021
lastupdated: "2021-11-10"

keywords: troubleshoot software, tgz, helm charts, cloud pak, terraform

subcollection: sell

content-type: troubleshoot

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:external: target="_blank" .external}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:beta: .beta}

# Why can't I validate or publish my software? 
{: #tgz-import-error}
{: troubleshoot}

You imported your software package as a `.tgz` file from the Software tab in Partner Center, but you can't validate it. In your Schematics logs, the following message is displayed:   
{: tsSymptoms}

`Error: Failed SHA validation, reload version to update the Digest`

When you access your product in the private catalog, the following message is also displayed: 

`Package required: We couldn't find the URL for your package. Verify that the package exists and that the access secret is valid.`

These errors typically occur because the contents of your software package were changed after you imported the package. 
{: tsCauses}

Reset the software package by reloading the version of your software that you're trying to onboard. For more information, see [Reloading a software version](/docs/sell?topic=sell-software-reload). 
{: tsResolve}

