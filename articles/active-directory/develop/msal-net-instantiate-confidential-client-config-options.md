---
title: Creare un'istanza di un'app client riservato con le opzioni (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come creare un'istanza di un'applicazione client riservata con opzioni di configurazione usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544080"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client riservata con opzioni di configurazione tramite MSAL.NET

Questo articolo descrive come creare un'istanza di un [applicazione client riservata](msal-client-applications.md) usando Microsoft Authentication Library per .NET (MSAL.NET).  L'applicazione viene creata un'istanza con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, è necessario innanzitutto [registrare](quickstart-register-app.md) , in modo che l'app può essere integrato con la piattaforma delle identità Microsoft. Dopo la registrazione, potrebbe essere necessario le informazioni seguenti (che sono reperibile nel portale di Azure):

- L'ID client (una stringa che rappresenta un GUID)
- URL di provider di identità (l'istanza denominata) e i destinatari di accesso per l'applicazione. Questi due parametri sono noti collettivamente come autorità.
- L'ID tenant se si sta scrivendo un'applicazione line of business esclusivamente per l'organizzazione (anche denominati applicazione a tenant singolo).
- Il segreto dell'applicazione (stringa segreto client) o il certificato (di tipo X509Certificate2) se si tratta di un'app client riservato.
- Per le app web e talvolta per le app client pubblico (in particolare quando l'app deve usare un broker), verranno inoltre impostate l'URI di reindirizzamento in cui il provider di identità contatterà back dell'applicazione con i token di sicurezza.

## <a name="configure-the-application-from-the-config-file"></a>Configurare l'applicazione dal file di configurazione
Il nome delle proprietà delle opzioni in MSAL.NET corrisponde al nome delle proprietà del `AzureADOptions` in ASP.NET Core, pertanto, non devi scrivere codice glue.

Configurazione di un'applicazione ASP.NET Core è descritta in un *appSettings. JSON* file:

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

A partire da MSAL.NET v3.x, è possibile configurare l'applicazione client riservato dal file config. Le classi correlate alla configurazione di app si trovano nel `Microsoft.Identity.Client.AppConfig` dello spazio dei nomi.

Nella classe in cui si desidera configurare e creare istanze dell'applicazione, è necessario dichiarare un `ConfidentialClientApplicationOptions` oggetto.  Associare la configurazione letta dall'origine (incluso il file appconfig.json) per l'istanza delle opzioni dell'applicazione:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

In questo modo il contenuto della sezione "AzureAD" del *appSettings. JSON* file da associare alla proprietà corrispondenti del `ConfidentialClientApplicationOptions` oggetto.  Successivamente, compilare un `ConfidentialClientApplication` oggetto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Aggiungere la configurazione di runtime
In un'applicazione client riservata, in genere è una cache per ogni utente. È pertanto necessario ottenere la cache associata all'utente e informare il compilatore di applicazioni che si desidera utilizzarla. Nello stesso modo, potrebbe essere un URI di reindirizzamento calcolato in modo dinamico. In questo caso il codice è il seguente:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

