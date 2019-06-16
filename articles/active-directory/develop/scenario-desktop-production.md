---
title: 'App desktop che chiama le API web (passare alla produzione): piattaforma delle identità Microsoft'
description: Informazioni su come creare un'app Desktop di chiamate di web API (passare alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111193"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>App desktop che chiama web API: passare alla produzione

Questo articolo fornisce informazioni dettagliate per migliorare ulteriormente l'applicazione e spostarlo nell'ambiente di produzione.

## <a name="handling-errors-in-desktop-applications"></a>Gestione degli errori nelle applicazioni desktop

In flussi diversi, si è appreso come gestire gli errori per i flussi invisibile all'utente (come illustrato nei frammenti di codice). Si è visto inoltre che vi sono casi in cui l'interazione è necessaria (consenso incrementale e l'accesso condizionale).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Come ottenere il consenso dell'utente anticipo per più risorse

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma delle identità Microsoft, ma non per Azure Active Directory (Azure AD) B2C. Azure AD B2C supporta solo il di consenso dell'amministratore, non il consenso dell'utente.

L'endpoint di Microsoft identity platform (v2.0) non consente di ottenere un token per diverse risorse in una sola volta. Pertanto, il `scopes` parametro può contenere solo gli ambiti per una singola risorsa. È possibile assicurarsi che l'utente acconsente preventivamente a numerose risorse usando il `extraScopesToConsent` parametro.

Ad esempio, se si hanno due risorse, che hanno due ambiti ogni:

- `https://mytenant.onmicrosoft.com/customerapi` -con 2 ambiti `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -con 2 ambiti `vendor.read` e `vendor.write`

È consigliabile usare la `.WithAdditionalPromptToConsent` modificatore contenente il `extraScopesToConsent` parametro.

Ad esempio:

```CSharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Questa chiamata recupera un token di accesso per la prima API web.

Quando è necessario chiamare l'API web secondo, è possibile chiamare:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Account personale Microsoft richiede reconsenting ogni volta che viene eseguita l'app

Per gli utenti di account personali Microsoft, reprompting il consenso per ogni chiamata di native client (app desktop o per dispositivi mobili) per l'autorizzazione è il comportamento previsto. Identità del client nativo è intrinsecamente non sicura (contrariamente applicazione client riservata cui scambiare una chiave privata con la piattaforma Microsoft Identity per dimostrare la propria identità). La piattaforma delle identità Microsoft ha scelto di attenuare questo qui per i servizi consumer richiedendo all'utente il consenso, ogni volta che l'applicazione è autorizzata.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
