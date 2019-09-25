---
title: App desktop che chiama le API Web (sposta in produzione)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app desktop che chiama le API Web (passa alla produzione)
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
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268329"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>App desktop che chiama le API Web-passa all'ambiente di produzione

Questo articolo fornisce informazioni dettagliate per migliorare ulteriormente l'applicazione e spostarla nell'ambiente di produzione.

## <a name="handling-errors-in-desktop-applications"></a>Gestione degli errori nelle applicazioni desktop

Nei diversi flussi si è appreso come gestire gli errori per i flussi Silent (come illustrato nei frammenti di codice). Si è inoltre notato che esistono casi in cui è necessaria l'interazione (consenso incrementale e accesso condizionale).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Come ottenere il consenso dell'utente in anticipo per diverse risorse

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure Active Directory (Azure AD) B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

L'endpoint della piattaforma Microsoft Identity (v 2.0) non consente di ottenere un token per più risorse contemporaneamente. Pertanto, il `scopes` parametro può contenere solo ambiti per una singola risorsa. È possibile verificare che l'utente preacconsente a diverse risorse tramite il `extraScopesToConsent` parametro.

Ad esempio, se si dispone di due risorse, che hanno due ambiti ciascuno:

- `https://mytenant.onmicrosoft.com/customerapi`-con due ambiti `customer.read` e`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-con due ambiti `vendor.read` e`vendor.write`

Usare il `.WithAdditionalPromptToConsent` modificatore con il `extraScopesToConsent` parametro.

Ad esempio:

### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>In MSAL per iOS e macOS

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];
    
NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
    
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]
        
let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
        
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Questa chiamata otterrà un token di accesso per la prima API Web.

Quando è necessario chiamare la seconda API Web, è possibile chiamare `AcquireTokenSilent` l'API:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>L'account personale Microsoft richiede la riautorizzazione ogni volta che viene eseguita l'app

Per gli utenti di account personali Microsoft, la richiesta di consenso per ogni chiamata di Native Client (desktop/mobile app) per autorizzare è il comportamento previsto. L'identità di Native Client è intrinsecamente non sicura (contrariamente alle applicazioni client riservate che scambiano un segreto con la piattaforma di identità Microsoft per dimostrare la propria identità). La piattaforma di identità Microsoft ha scelto di mitigare questa insicurezza per i servizi consumer richiedendo il consenso dell'utente, ogni volta che l'applicazione è autorizzata.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
