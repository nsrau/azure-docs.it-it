---
title: Creare un'istanza di un'app client riservata con opzioni (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come creare un'istanza di un'applicazione client riservata con le opzioni di configurazione utilizzando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a05959311b7f62f88a7b474b907982e005b98b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532622"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client riservata con le opzioni di configurazione usando MSAL.NET

Questo articolo descrive come creare un'istanza di un' [applicazione client riservata](msal-client-applications.md) utilizzando Microsoft Authentication Library per .net (MSAL.NET).  Viene creata un'istanza dell'applicazione con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, è prima necessario [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft. Dopo la registrazione, è possibile che siano necessarie le informazioni seguenti (disponibili nella portale di Azure):

- ID client (una stringa che rappresenta un GUID)
- URL del provider di identità (denominato istanza) e destinatari dell'accesso per l'applicazione. Questi due parametri sono collettivamente noti come autorità.
- ID tenant se si sta scrivendo un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione a tenant singolo).
- Il segreto dell'applicazione (stringa del segreto client) o il certificato (di tipo X509Certificate2) se si tratta di un'app client riservata.
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), è anche possibile impostare il redirectUri in cui il provider di identità contatterà l'applicazione con i token di sicurezza.

## <a name="configure-the-application-from-the-config-file"></a>Configurare l'applicazione dal file di configurazione
Il nome delle proprietà delle opzioni in MSAL.NET corrisponde al nome delle proprietà del `AzureADOptions` in ASP.NET Core, quindi non è necessario scrivere codice Glue.

Una configurazione dell'applicazione ASP.NET Core viene descritta in un file *appSettings. JSON* :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A partire da MSAL.NET V3. x, è possibile configurare l'applicazione client riservata dal file di configurazione.

Nella classe in cui si desidera configurare e creare un'istanza dell'applicazione, è necessario dichiarare un `ConfidentialClientApplicationOptions` oggetto.  Associare la configurazione letta dall'origine (incluso il file AppConfig. Json) all'istanza delle opzioni dell'applicazione, usando il `IConfigurationRoot.Bind()` metodo dal [pacchetto NuGet Microsoft. Extensions. Configuration. Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Ciò consente di associare il contenuto della sezione "AzureAD" del file *appSettings. JSON* alle proprietà corrispondenti dell' `ConfidentialClientApplicationOptions` oggetto.  Successivamente, compilare un `ConfidentialClientApplication` oggetto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Aggiungere la configurazione di runtime
In un'applicazione client riservata, in genere è presente una cache per utente. Pertanto, sarà necessario ottenere la cache associata all'utente e informare il generatore di applicazioni che si desidera utilizzarla. Allo stesso modo, è possibile che si disponga di un URI di reindirizzamento calcolato dinamicamente. In questo caso il codice è il seguente:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

