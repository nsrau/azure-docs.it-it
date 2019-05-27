---
title: App desktop di chiamate di web API (configurazione del codice) - piattaforma delle identità Microsoft
description: Informazioni su come creare un'app Desktop di chiamate di web API (configurazione del codice dell'app)
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
ms.openlocfilehash: bc0042d6392891e8282c563afea2212031a0f49a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121877"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>App desktop che chiama l'API - configurazione del codice web

Ora che è stato creato l'applicazione, si apprenderà come configurare il codice con le coordinate dell'applicazione.

## <a name="msal-libraries"></a>Librerie MSAL

La libreria MSAL sola il supporto di applicazioni desktop oggi è MSAL.NET

## <a name="public-client-application"></a>Applicazione client pubblica

Da un punto di vista di codice, applicazioni desktop sono le applicazioni client pubblico e per tale motivo si creerà e manipolare MSAL.NET `IPublicClientApplication`. Anche in questo caso cose sarà leggermente diversa se si utilizza l'autenticazione interattiva o No.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Esclusivamente dal codice

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti di accesso nel cloud pubblico di Microsoft Azure, con un lavoro e dell'istituto di istruzione o un account Microsoft personale.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se si prevede di usare l'autenticazione interattiva, come nell'esempio precedente, si desidera utilizzare il `.WithRedirectUri` modificatore:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="using-configuration-files"></a>Usando i file di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che potrebbe essere compilato in a livello di codice o letto da un file di configurazione

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configurazione di più elaborato

È possibile elaborare l'applicazione di compilazione aggiungendo un numero di modificatori. Ad esempio, se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (qui governo degli Stati Uniti), è possibile scrivere:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contiene anche un modificatore per ad FS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si vuole acquisire i token per un tenant di Azure AD B2C, possibile specificare il tenant come illustrato nel frammento di codice seguente:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Altre informazioni

Per altre informazioni su come configurare un'applicazione desktop MSAL.NET:

- Per un elenco di tutti i modificatori disponibili nel `PublicClientApplicationBuilder`, vedere la documentazione di riferimento [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Per la descrizione di tutte le opzioni esposte nella `PublicClientApplicationOptions` visualizzare [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), nella documentazione di riferimento

## <a name="complete-example-with-configuration-options"></a>Esempio completo con le opzioni di configurazione

Si immagini un'applicazione console .NET Core che ha i seguenti elementi `appsettings.json` file di configurazione:

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

È necessario disporre di codice per leggere il file usando .NET forniti framework di configurazione.

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

A questo punto, per creare l'applicazione, è semplicemente necessario scrivere il codice seguente:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
           .Build();
```

e prima della chiamata ai `.Build()` metodo, è possibile ignorare la configurazione con le chiamate a `.WithXXX` metodi come illustrato in precedenza.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisire un token per un'app desktop](scenario-desktop-acquire-token.md)
