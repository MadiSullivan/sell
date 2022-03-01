---

copyright:

  years: 2018, 2022

lastupdated: "2022-02-11"

keywords: access token, client ID, Access Manage page, authentication flow 

subcollection: sell

---

{{site.data.keyword.attribute-definition-list}}

# Developing an authentication flow
{: #step4-iam}

The process to onboard and manage services through the resource management console is limited to existing services. New services must be onboarded through Partner Center. To onboard a new service, see [Getting set up to sell services](/docs/sell?topic=sell-get-started).
{: important}

When you define your service, the Access Manage page in the resource management console lists your {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM) client ID and secret, your service ID, and your API key. Now you're ready to use those values to develop an authentication flow.
{: shortdesc}

## Before you begin
{: #iam-pre-reqs}

Make sure that you completed the [getting started tutorial](/docs/sell?topic=sell-get-started#get-started), and you're approved to deliver your service.

## Derive your IAM redirect URI
{: #redirect-uri}

When you define your service in the resource management console, you generate a client ID, but note that you likely don't have a redirect URI at the time. A client ID that is set to false is created by IAM. Until you return to the resource management console with your redirect URI, you won't have a true client ID.

The good news is that in the previous development step, you developed an OSB and hosted it (You probably saw IAM values in the sample broker code). The `redirect_uri` is usually the host url where the app lives with some additional url that can handle the authentication and authorization.

 The following examples show redirect URIs:

```text
https://<myapp>.cloud.ibm.com/integrate/auth/callback
http://localhost:3000/auth/callback <-- for testing locally
```

Return to the resource management console and add your redirect URI to the IAM tab:

1. Sign in to the console.
2. Grab your redirect URI.
3. Return to the resource management console.
4. From the IAM tab, paste your redirect URI into the **Redirect URI** field.
5. Click **Update Client ID** to update your Client ID.

You now have a client ID that understands your redirect URI and is set to true! You can use that client ID in the next steps to develop your OAuth flow.

## Develop your OAuth flow for authentication
{: #oauth}


**Authentication - Step 0:** Find IAM regional endpoint for UI login closer to your deployed application by calling `https://iam.cloud.ibm.com/identity/.well-known/openid-configuration`.

```bash
curl -X GET \
  https://iam.cloud.ibm.com/identity/.well-known/openid-configuration
```

```text
HTTP/1.1 200 OK
Content-Type: application/json
{
  "issuer": "https://iam.cloud.ibm.com/identity",
  "authorization_endpoint": "https://iam-region2.cloud.ibm.com/identity/authorize",
  "token_endpoint": "https://iam-region2.cloud.ibm.com/identity/token",
...
}
```

This request can be done once when the application is started and again if the `authorization_endpoint` fails. You now are able to cache the `authorization_endpoint` value for a short amount of time and refresh after the cache expired or an error is encountered.


**Authentication - Step 1:** When a user navigates to your `dashboard_url`, redirect browser to `<authorization_endpoint>?client_id=<your-client-id>&redirect_uri=<your-redirect-uri>&response-type=code&state=<your-resource-instance-id>`

* If a user is logged in, they are immediately redirected. The browser does a call back to redirect the URI to provide a `code` response parameter and `state` value. 
* If a user is not logged in, a log in prompt will appear. After a successful log in, the browser does a call back to redirect the URI to provide a `code` response parameter and `state` value.

**Authentication - Step 2:** Exchange the code for an access token calling

### POST <token_endpoint>
{: #token-post}

#### Headers:
{: #headers1}

- Authorization: Basic *[client id]:[client secret]*
- Content-Type: application/x-www-form-urlencoded
- Accept: application/json

#### With parameters:
{: #parameters1}

- client_id=*[client id]*
- client_secret=*[client secret]*
- grant_type=authorization_code
- response_type=cloud_iam
- redirect_uri=*[same URI as redirect_uri from step 1]*
- code=*[code from callback]*

```bash
curl -k -X POST \
  -u "<your-client-id>:<your-client-secret>" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  --data-urlencode "client_id=<your-client-id>" \
  --data-urlencode "client_secret=<your-client-secret>" \
  --data-urlencode "grant_type=authorization_code" \
  --data-urlencode "response_type=cloud_iam" \
  --data-urlencode "code=<code-from-the-callback>" \
  --data-urlencode "redirect_uri=<redirect_uri>" \
  "https://iam-region2.cloud.ibm.com/identity/token"
```
{: codeblock}

### Response:
{: #response1}

```text
{
  "access_token": "eyJraWQiOiI......XmpBTIDdR5w",
  "refresh_token": "SPrXw5tBE3......KBQ+luWQVY=",
  "token_type": "Bearer",
  "expires_in": 3600,
  "expiration": 1473188353
}
```
{: codeblock}

Make sure to store the user's access_token returned in this response as it is used during user authorization next.

See the example in our [sample brokers](https://github.com/IBM/sample-resource-service-brokers){: external}.

## Validate the user authorization
{: #validate}

1. Communicate with IAM to get an access token for an API key.
2. Validate authorization for the user to the service instance (/v2/authz POST).

### Authorization - Step 1: Get an {{site.data.keyword.Bluemix_notm}} IAM token by using an API Key.
{: #iam_token_using_api_key}

### POST /identity/token
{: #post}

#### Headers:
{: #headers2}

- Content-Type: application/x-www-form-urlencoded
- Accept: application/json

#### With parameters:
{: #parameters2}

- grant_type=urn:ibm:params:oauth:grant-type:apikey
- response_type=cloud_iam
- apikey=*[Api key]*

```bash
curl -k -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  --data-urlencode "grant_type=urn:ibm:params:oauth:grant-type:apikey" \
  --data-urlencode "response_type=cloud_iam" \
  --data-urlencode "apikey=<apikey>" \
  "https://iam.cloud.ibm.com/identity/token"


```
{: codeblock}

### Response:
{: #response2}

```text
{
  "access_token": "eyJhbGciOiJIUz......sgrKIi8hdFs",
  "refresh_token": "SPrXw5tBE3......KBQ+luWQVY=",
  "token_type": "Bearer",
  "expires_in": 3600,
  "expiration": 1473188353
}
```
{: codeblock}

This token is valid for 1 hour, and can be reused as many times as needed during the 1 hour time frame. It is highly recommended that this token is cached as to avoid doing this request for every access to the `dashboard_url`.
{: note}


### Authorization - Step 2: Validate authorization for the user to the service instance (/v2/authz POST)
{: #step-2-authorization}

Now that you authenticated the user and have your own access token, you need to validate that the user is able to access the service dashboard. First, you need a few pieces of information that are included in the user's access token that you decode in step 2.1. Then, you use that information to call IAM to check whether the user is authorized to access the dashboard in step 2.2.

**Step 2.1**: Decode the user's access token (returned during `**Authentication - Step 2:** Exchange the code for an access token` found in the preceding section.)
   The access token is a JWT token that can be decoded by using any JWT-compliant library. For example, see the library included in our [sample broker code](https://github.com/IBM/sample-resource-service-brokers){: external}.
   After the token is decoded, the format is as shown in the following section; you extract the `iam_id` and `scope` fields, which are used in the next step:

```text
{
  "iam_id": "IBMid-XXXXXXX",
  "id": "IBMid-XXXXXXX",
  "realmid": "IBMid",
  "identifier": "XXXXXXX",
  "given_name": "Don",
  "family_name": "Quixote",
  "name": "Don Quixote",
  "email": "quixote@email.com",
  "sub": "quixote@email.com",
  "account": {
    "bss": "123123123123123"
  },
  "iat": 1522114004,
  "exp": 1522117604,
  "iss": "https://iam.cloud.ibm.com/identity",
  "grant_type": "urn:ibm:params:oauth:grant-type:apikey",
  "scope": "openid <your serviceName>",
  "client_id": "bx",
  "acr": 1,
  "amr": [
    "pwd"
  ]
}
```

**Step 2.2**: Call IAM to check whether the user is authorized to access the dashboard

```bash
curl -X POST \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "Authorization: <access token from step 1>" \
  -d '[ \
    { \
      "subject" : \
      { \
        "attributes": \
        { \
          "id": "<iam_id field value from user's token>", \
          "scope": "<scope field value from user's token>" \
        } \
      }, \
      "resource" : \
      { \
        "crn" : "<resource instance CRN>" \
      }, \
      action : <your service name> + ".dashboard.view" \
    } \
  ]' \
  https://iam.cloud.ibm.com/v2/authz
```

See the example in our [sample brokers](https://github.com/IBM/sample-resource-service-brokers).

## IAM token scoping for third-party adopters
{: #token_scoping}

User access tokens that are created with your Client ID can be used only to access your service APIs. Requests to other cloud APIs that use this token results in access denied, even if the user has an appropriate policy configured.

As part of third-party integration, token scoping is being used to ensure that tokens have the minimal access scope that is needed to accomplish the user's goals. To facilitate this, IAM tokens access is based on the Client ID that created the token. If an IAM token was created by a third-party service, an end user can't run certain APIs and functions, even if the user has an appropriate policy configured.

The impact on authorizations (all calls to `https://iam.cloud.ibm.com/v2/authz`) is the need to pass down `scope` information in the subject. This information is contained inside an IAM token (base64 encoded) in the `scope` claim.

The following section is an example of what is added in the authorization call:
```text
  [
   {  Headers
   `Authorization` -> a jwt token representing a 3rd party service and/or dashboard
   `Transaction-ID` -> "a unique guid lets us help trace the request end to end"
   `Accept` -> `application/vnd.authz.v2+json`
   `Content-Type` -> `application/json`

      Body
      "subject":{
         "attributes":{
            "id":"IBMid-123",
            "scope":"libraryservice openid"
         }
      },
      "action":"libraryservice.books.read",
      "resource":{
         "attributes":{
            "serviceName":"libraryservice",
            "serviceInstance":"12345",
            "accountId":"123456789"
         }
      }
   }
]
```
{: codeblock}

This is applicable to all usages (`user, serviceId, crn`) and all `subject.attributes` need a scope.

## Next steps
{: #cis5-test}

Now it's time to pull everything together! Return to the resource management console to publish your service in limited visibility and validate your service in the catalog. For more information, see [Publishing and testing your service](/docs/sell?topic=sell-step5-pubtest#step5-pubtest).
