---
page_type: sample
languages:
- java
- powershell
products:
- active-directory
- verifiable credentials
description: "A code sample demonstrating issuance and verification of verifiable credentials."
urlFragment: "active-directory-verifiable-credentials-java"
---
# Verifiable Credentials Code Sample

This code sample demonstrates how to use Microsoft's Azure Active Directory Verifiable Credentials preview to issue and consume verifiable credentials. 

## About this sample

Welcome to Azure Active Directory Verifiable Credentials. In this sample, we'll teach you to issue your first verifiable credential: a Verified Credential Expert Card. You'll then use this card to prove to a verifier that you are a Verified Credential Expert, mastered in the art of digital credentialing. The sample uses the preview REST API which supports ID Token hints to pass a payload for the verifiable credential.

> **Important**: Azure Active Directory Verifiable Credentials is currently in public preview. This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Contents

The project is divided in 2 parts, one for issuance and one for verifying a verifiable credential. Depending on the scenario you need you can remove 1 part. To verify if your environment is completely working you can use both parts to issue a verifiedcredentialexpert VC and verify that as well.


| Issuance | |
|------|--------|
| src\main\resources\static\issuer.html|The basic webpage containing the javascript to call the APIs for issuance. |
| src\main\java\com\verifiablecredentials\javaaadvcapiidtokenhint\controller\IssuerController.java | This is the controller which contains the API called from the webpage. It calls the REST API after getting an access token through MSAL. |
| issuance_request_config.json | The sample payload send to the server to start issuing a VC. |

| Verification | |
|------|--------|
| src\main\resources\static\verifier.html | The website acting as the verifier of the verifiable credential.
| src\main\java\com\verifiablecredentials\javaaadvcapiidtokenhint\controller\VerifierController.java | This is the controller which contains the API called from the webpage. It calls the REST API after getting an access token through MSAL and helps verifying the presented verifiable credential.
| presentation_request_config.json | The sample payload send to the server to start issuing a vc.

## Setup

Before you can run this sample make sure your environment is setup correctly, follow the instructions in the documentation [here](https://aka.ms/vcsample)

### create application registration
Run the [Configure.PS1](./AppCreationScripts/AppCreationScripts.md) powershell script in the AppCreationScripts directory or follow these manual steps to create an application registrations, give the application the correct permissions so it can access the Verifiable Credentials Request REST API:

Register an application in Azure Active Directory: 
1. Sign in to the Azure portal using either a work or school account or a personal Microsoft account.
2. Navigate to the Microsoft identity platform for developers App registrations page.
3.	Select New registration
    -  In the Name section, enter a meaningful application name for your issuance and/or verification application
    - In the supported account types section, select Accounts in this organizational directory only ({tenant name})
    - Select Register to create the application
4.	On the app overview page, find the Application (client) ID value and Directory (tenant) ID and record it for later.
5.	From the Certificates & secrets page, in the Client secrets section, choose New client secret:
    - Type a key description (for instance app secret)
    - Select a key duration.
    - When you press the Add button, the key value will be displayed, copy and save the value in a safe location.
    - You’ll need this key later to configure the sample application. This key value will not be displayed again, nor retrievable by any other means, so record it as soon as it is visible from the Azure portal.
6.	In the list of pages for the app, select API permissions
    - Click the Add a permission button
    - Search for APIs in my organization for 3db474b9-6a0c-4840-96ac-1fceb342124f or Verifiable Credential and click the “Verifiable Credential Request Service”
    - Click the “Application Permission” and expand “VerifiableCredential.Create.All”
    - Click Grant admin consent for {tenant name} on top of the API/Permission list and click YES. This allows the application to get the correct permissions
![Admin concent](ReadmeFiles/AdminConcent.PNG)

## Setting up and running the sample
To run the sample, clone the repository, compile & run it. It's callback endpoint must be publically reachable, and for that reason, use `ngrok` as a reverse proxy to reach your app.

```Powershell
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials-java.git
cd active-directory-verifiable-credentials-java\1-java-api-idtokenhint
```

### Create your credential
To use the sample we need a configured Verifiable Credential in the azure portal.
In the project directory CredentialFiles you will find the `VerifiedCredentialExpertDisplay.json` file and the `VerifiedCredentialExpertRules.json` file. Use these 2 files to create your own VerifiedCredentialExpert credential. 
Before you upload the files, you need to modify the `VerifiedCredentialExpertRules.json` file.
If you navigate to your [Verifiable Credentials](https://portal.azure.com/#blade/Microsoft_AAD_DecentralizedIdentity/InitialMenuBlade/issuerSettingsBlade) blade in azure portal, you can copy the Decentralized identifier (DID) string (did:ion..) and modify the value after "iss" on line 12. Save the file and follow the instructions how to create your first verifiable credential.

You can find the instructions on how to create a Verifiable Credential in the azure portal [here](https://aka.ms/didfordevs)

Make sure you copy the value of the credential URL after you created the credential in the portal. 

Copy the URL in the `CredentialManifest` part of the `run.cmd` and/or `run.sh`. If you plan to use docker, copy it to `docker-run.cmd` and/or `docker-run.sh`. 
You need to manually copy your Microsoft AAD Verifiable Credential service created Decentralized Identifier (did:ion..) value from this page as well and paste that in the `run.cmd` and/or `run.sh` file for `IssuerAuthority` and `VerifierAuthority`. If you plan to use docker, copy it to `docker-run.cmd` and/or `docker-run.sh`

### API Payloads
The API is called with special payloads for issuing and verifying verifiable credentials. The sample payload files are modified by the sample code by copying the correct values defined in the `appliction.properties` file and set as environment variables before you run the app.
If you want to modify the payloads `issuance_request_config.json` and `presentation_request_config.json` files yourself, make sure you comment out the code overwriting the values in the VerifierController.java and IssuerController.java files. The code overwrites the Authority, Manifest and trustedIssuers values. The callback URI is modified in code to match your hostname.

The file [run.cmd](run.cmd) is a template for setting all environment variables and running your Java Springbot application.
Make sure you change the values for `AADVC.TenantId`, `AADVC.ClientID` and `AADVC.ClientSecret` and set the to the values from th eapp registration you created above. Then, there are two other variables that references files used as templates for issuance and verification. You need to update them too. 

## Running the sample

In order to build & run the sample, you need to have the [Java SDK](https://www.oracle.com/java/technologies/downloads/) and [Maven](https://maven.apache.org/download.cgi) installed locally. The sample has been tested with Java 11. There is also a file called [build.cmd](build.cmd) that can be used and that contains the same command.

1. Open a command prompt and run the following command:
```Powershell
mvn clean package -DskipTests
```

1. After you have edited the file [run.cmd](run.cmd), start the Java Springbot app by running this in the command prompt
```Powershell
.\run.cmd
```

1. Using a different command prompt, run ngrok to set up a URL on 8080. You can install ngrok globally from this [link](https://ngrok.com/download).
```Powershell
ngrok http 8080
```
1. Open the HTTPS URL generated by ngrok.
![API Overview](ReadmeFiles/ngrok-url-screen.png)
The sample dynamically copies the hostname to be part of the callback URL, this way the VC Request service can reach your sample web application to execute the callback method.

1. Select GET CREDENTIAL

1. In Authenticator, scan the QR code. 
> If this is the first time you are using Verifiable Credentials the Credentials page with the Scan QR button is hidden. You can use the `add account` button. Select `other` and scan the QR code, this will enable the preview of Verifiable Credentials in Authenticator.
6. If you see the 'This app or website may be risky screen', select **Advanced**.
1. On the next **This app or website may be risky** screen, select **Proceed anyways (unsafe)**.
1. On the Add a credential screen, notice that:

  - At the top of the screen, you can see a red **Not verified** message.
  - The credential is based on the information you uploaded as the display file.

9. Select **Add**.

## Verify the verifiable credential by using the sample app
1. Navigate back and click on the Verify Credential link
2. Click Verify Credential button
3. Scan the QR code
4. select the VerifiedCredentialExpert credential and click allow
5. You should see the result presented on the screen.

## About the code
Since the API is now a multi-tenant API it needs to receive an access token when it's called. 
The endpoint of the API is https://beta.did.msidentity.com/v1.0/{YOURTENANTID}/verifiablecredentials/request 

To get an access token we are using MSAL as library. MSAL supports the creation and caching of access token which are used when calling Azure Active Directory protected resources like the verifiable credential request API.
Typicall calling the libary looks something like this:
```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        clientId,
        ClientCredentialFactory.createFromSecret(clientSecret))
        .authority(authority)
        .build();
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(scope))
        .build();
```
And creating an access token:
```Java
CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);
IAuthenticationResult result = future.get();
```
> **Important**: At this moment the scope needs to be: **3db474b9-6a0c-4840-96ac-1fceb342124f/.default** This might change in the future

Calling the API looks like this:
```Java
WebClient client = WebClient.create();
WebClient.ResponseSpec responseSpec = client.post()
                                            .uri( endpoint )
                                            .header(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                                            .header("Authorization", "Bearer " + accessToken)
                                            .accept(MediaType.APPLICATION_JSON)
                                            .body(BodyInserters.fromObject(payload))
                                            .retrieve();
String responseBody = responseSpec.bodyToMono(String.class).block();    
```

## Troubleshooting

### Did you forget to provide admin consent? This is needed for confidential apps
If you get an error when calling the API `Insufficient privileges to complete the operation.`, this is because the tenant administrator has not granted permissions
to the application. See step 6 of 'Register the client app' above.

You will typically see, on the output window, something like the following:

```Json
Failed to call the Web Api: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<a guid>",
      "date": "<date>"
    }
  }
}
```

### Understanding what's going on
As a first source of information, the Java sample will trace output into the console window of all HTTP calls it receives. Then a good tip is to use Edge/Chrome/Firefox dev tools functionality found under F12 and watch the Network tab for traffic going from the browser to the Java app.

## Best practices
When deploying applications which need client credentials and use secrets or certificates the more secure practice is to use certificates. If you are hosting your application on azure make sure you check how to deploy managed identities. This takes away the management and risks of secrets in your application.
You can find more information here:
- [Integrate a daemon app with Key Vault and MSI](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/3-Using-KeyVault)


## More information

For more information, see MSAL.NET's conceptual documentation:

- [Quickstart: Register an application with the Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
- [Quickstart: Configure a client application to access web APIs](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)
- [Acquiring a token for an application with client credential flows](https://aka.ms/msal-net-client-credentials)
