---
title: App desktop che chiama le API Web (configurazione del codice)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app desktop che chiama le API Web (configurazione del codice dell'app)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268424"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>App desktop che chiama API Web-configurazione del codice

Ora che è stata creata l'applicazione, si apprenderà come configurare il codice con le coordinate dell'applicazione.

## <a name="msal-libraries"></a>Librerie MSAL

L'unica libreria MSAL che supporta le applicazioni desktop su più piattaforme oggi è MSAL.NET.

MSAL per iOS e macOS supporta le applicazioni desktop in esecuzione solo in macOS. 

## <a name="public-client-application-with-msalnet"></a>Applicazione client pubblica con MSAL.NET

Dal punto di vista del codice, le applicazioni desktop sono applicazioni client pubbliche ed è per questo motivo che è possibile creare e `IPublicClientApplication`modificare MSAL.NET. Anche in questo caso, è leggermente diverso se si usa l'autenticazione interattiva.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Esclusivamente per codice

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti che accedono all'Microsoft Azure cloud pubblico, un account aziendale o dell'Istituto di istruzione o un account Microsoft personale.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se si intende usare l'autenticazione interattiva o il flusso del codice del dispositivo, come illustrato in precedenza, si `.WithRedirectUri` vuole usare il modificatore:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Uso dei file di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che può essere compilato a livello di codice o letto da un file di configurazione

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

Per elaborare la compilazione di applicazioni, è possibile aggiungere un numero di modificatori. Ad esempio, se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (in questo governo degli Stati Uniti), è possibile scrivere:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contiene anche un modificatore per ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si desidera acquisire i token per un tenant di Azure AD B2C, è possibile specificare il tenant come illustrato nel frammento di codice seguente:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Altre informazioni

Per altre informazioni su come configurare un'applicazione desktop MSAL.NET:

- Per l'elenco di tutti i modificatori disponibili `PublicClientApplicationBuilder`in, vedere la documentazione di riferimento [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Per la descrizione di tutte le opzioni esposte in `PublicClientApplicationOptions` vedere [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), nella documentazione di riferimento

## <a name="complete-example-with-configuration-options"></a>Esempio completo con le opzioni di configurazione

Si supponga di disporre di un'applicazione console .NET Core `appsettings.json` con il seguente file di configurazione:

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

Il codice per la lettura di questo file è minimo con il Framework di configurazione .NET fornito.

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
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

  // Read the auth and graph endpoint config
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

A questo punto, per creare l'applicazione, è sufficiente scrivere il codice seguente:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

prima della chiamata al `.Build()` metodo, è possibile eseguire l'override della configurazione con le chiamate ai `.WithXXX` metodi, come illustrato in precedenza.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Applicazione client pubblica con MSAL per iOS e macOS

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti che accedono all'Microsoft Azure cloud pubblico, un account aziendale o dell'Istituto di istruzione o un account Microsoft personale.

### <a name="quick-configuration"></a>Configurazione rapida

Objective-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

Per elaborare la compilazione di applicazioni, è possibile aggiungere un numero di modificatori. Ad esempio, se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (in questo governo degli Stati Uniti), è possibile scrivere:

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

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token per un'app desktop](scenario-desktop-acquire-token.md)
