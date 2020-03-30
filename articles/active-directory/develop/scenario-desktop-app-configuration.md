---
title: Configurare app desktop che richiedono API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come configurare il codice di un'app desktop che chiama le API Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2ba69e6447c686230412c33e74196c4bb594e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611818"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>App desktop che chiama le API Web: configurazione del codice

Dopo aver creato l'applicazione, si apprenderà come configurare il codice con le coordinate dell'applicazione.

## <a name="microsoft-authentication-libraries"></a>Librerie di autenticazione Microsoft

Le seguenti librerie di autenticazione Microsoft (MSAL) supportano le applicazioni desktop.

  Microsoft Authentication Library | Descrizione
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Supporta la creazione di un'applicazione desktop in più piattaforme, ad esempio Linux, Windows e macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Supporta la creazione di un'applicazione desktop in più piattaforme.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Supporta la creazione di un'applicazione desktop in più piattaforme.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Supporta le applicazioni desktop che vengono eseguite solo su macOS.

## <a name="public-client-application"></a>Applicazione client pubblica

Dal punto di vista del codice, le applicazioni desktop sono applicazioni client pubbliche. La configurazione sarà un po' diversa a seconda che si utilizzi o meno l'autenticazione interattiva.

# <a name="net"></a>[.NET](#tab/dotnet)

È necessario compilare e `IPublicClientApplication`modificare MSAL.NET .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Esclusivamente dal codice

Il codice seguente crea un'istanza di un'applicazione client pubblica e accede agli utenti nel cloud pubblico di Microsoft Azure con un account aziendale o dell'istituto di istruzione o un account Microsoft personale.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se si intende usare l'autenticazione interattiva o `.WithRedirectUri` il flusso di codice del dispositivo, come illustrato in precedenza, usare il modificatore.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Usare i file di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che può essere compilata a livello di codice o letta da un file di configurazione.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

È possibile elaborare la compilazione dell'applicazione aggiungendo un numero di modificatori. Ad esempio, se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, ad esempio il governo degli Stati Uniti illustrato di seguito, è possibile scrivere:For example, if you want your application to be a multitenant application in a national cloud, such as US Government shown here, you could write:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contiene anche un modificatore per Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si desidera acquisire token per un tenant B2C di Azure Active Directory (Azure AD), specificare il tenant come illustrato nel frammento di codice seguente:Finally, if you want to acquire tokens for an Azure Active Directory (Azure AD) B2C tenant, specify your tenant as shown in the following code snippet:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Altre informazioni

Per altre informazioni su come configurare un'applicazione desktop MSAL.NET:

- Per un elenco di tutti `PublicClientApplicationBuilder`i modificatori disponibili in , vedere la documentazione di riferimento [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Per una descrizione di tutte `PublicClientApplicationOptions`le opzioni esposte in , vedere [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) nella documentazione di riferimento.

### <a name="complete-example-with-configuration-options"></a>Esempio completo con opzioni di configurazione

Si immagini un'applicazione console .NET Core con il file di configurazione seguente: `appsettings.json`

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Si dispone di poco codice da leggere in questo file utilizzando il file . Framework di configurazione fornito da NET:NET-provided configuration framework:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

A questo punto, per creare l'applicazione, scrivere il codice seguente:Now, to create your application, write the following code:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Prima della chiamata `.Build()` al metodo , è possibile `.WithXXX` eseguire l'override della configurazione con le chiamate ai metodi, come illustrato in precedenza.

# <a name="java"></a>[Java](#tab/java)

Di seguito è riportata la classe utilizzata negli esempi di sviluppo Java MSAL per configurare gli esempi: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[Macos](#tab/macOS)

Il codice seguente crea un'istanza di un'applicazione client pubblica e accede agli utenti nel cloud pubblico di Microsoft Azure con un account aziendale o dell'istituto di istruzione o un account Microsoft personale.

### <a name="quick-configuration"></a>Configurazione rapida

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

È possibile elaborare la compilazione dell'applicazione aggiungendo un numero di modificatori. Ad esempio, se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, ad esempio il governo degli Stati Uniti illustrato di seguito, è possibile scrivere:For example, if you want your application to be a multitenant application in a national cloud, such as US Government shown here, you could write:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisire un token per un'app desktop](scenario-desktop-acquire-token.md)
