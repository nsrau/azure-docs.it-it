---
title: Creare un'istanza di un'app client confidenziale (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'istanza di un'applicazione client riservata con opzioni di configurazione utilizzando la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084728"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client riservata con opzioni di configurazione utilizzando MSAL.NET

In questo articolo viene descritto come creare un'istanza di [un'applicazione client riservata](msal-client-applications.md) utilizzando Microsoft Authentication Library for .NET (MSAL.NET).  Viene creata un'istanza dell'applicazione con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, devi prima [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft. Dopo la registrazione, potrebbero essere necessarie le informazioni seguenti (che possono essere trovate nel portale di Azure):After registration, you may need the following information (which can be found in the Azure portal):

- L'ID client (una stringa che rappresenta un GUID)
- L'URL del provider di identità (denominato l'istanza) e il gruppo di destinatari di accesso per l'applicazione. Questi due parametri sono collettivamente noti come l'autorità.
- ID tenant se si scrive un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione single-tenant).
- Il segreto dell'applicazione (stringa del segreto client) o certificato (di tipo X509Certificate2) se si tratta di un'app client riservata.
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), devi anche impostare redirectUri in cui il provider di identità contatterà nuovamente l'applicazione con i token di sicurezza.

## <a name="configure-the-application-from-the-config-file"></a>Configurare l'applicazione dal file di configurazione
Il nome delle proprietà delle opzioni in MSAL.NET corrisponda `AzureADOptions` al nome delle proprietà di ASP.NET Core, pertanto non è necessario scrivere codice di associazione.

Una configurazione dell'applicazione ASP.NET Core è descritta in un file *appsettings.json:An* ASP.NET Core application configuration is described in an appsettings.json file:

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

A partire da MSAL.NET v3.x, è possibile configurare l'applicazione client riservata dal file di configurazione.

Nella classe in cui si desidera configurare e `ConfidentialClientApplicationOptions` creare un'istanza dell'applicazione, è necessario dichiarare un oggetto.  Associare la configurazione letta dall'origine (incluso il file appconfig.json) `IConfigurationRoot.Bind()` all'istanza delle opzioni dell'applicazione, utilizzando il metodo del [pacchetto Microsoft.Extensions.Configuration.Binder nuget](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

In questo modo il contenuto della sezione "AzureAD" del file *appsettings.json* deve essere associato alle proprietà corrispondenti dell'oggetto. `ConfidentialClientApplicationOptions`  Successivamente, creare `ConfidentialClientApplication` un oggetto:Next, build a object:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Aggiungere la configurazione di runtimeAdd runtime configuration
In un'applicazione client riservata, in genere si dispone di una cache per utente. Pertanto è necessario ottenere la cache associata all'utente e informare il generatore di applicazioni che si desidera utilizzarlo. Allo stesso modo, si potrebbe avere un URI di reindirizzamento calcolato dinamicamente. In questo caso il codice è il seguente:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

