---
title: Spostare l'app desktop che chiama le API Web nell'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come spostare un'app desktop che chiama API Web nell'ambiente di produzione
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80882880"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>App desktop che chiama le API Web: passa all'ambiente di produzione

Questo articolo illustra come spostare l'app desktop che chiama API Web nell'ambiente di produzione.

## <a name="handle-errors-in-desktop-applications"></a>Gestione degli errori nelle applicazioni desktop

Nei diversi flussi si è appreso come gestire gli errori per i flussi invisibile all'utente, come illustrato nei frammenti di codice. Si è inoltre notato che esistono casi in cui è necessaria l'interazione, ad esempio il consenso incrementale e l'accesso condizionale.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Ottenere il consenso dell'utente in anticipo per diverse risorse

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure Active Directory (Azure AD) B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Non è possibile ottenere un token per più risorse contemporaneamente con l'endpoint della piattaforma Microsoft Identity Platform (v 2.0). Il `scopes` parametro può contenere ambiti solo per una singola risorsa. È possibile verificare che l'utente preacconsente a diverse risorse tramite il `extraScopesToConsent` parametro.

È possibile, ad esempio, che siano presenti due risorse con due ambiti:

- `https://mytenant.onmicrosoft.com/customerapi` con gli ambiti `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` con gli ambiti `vendor.read` e `vendor.write`

In questo esempio, usare il `.WithAdditionalPromptToConsent` modificatore con il `extraScopesToConsent` parametro.

Ad esempio:

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
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

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Questa chiamata ottiene un token di accesso per la prima API Web.

Quando è necessario chiamare la seconda API Web, chiamare l' `AcquireTokenSilent` API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>L'account personale Microsoft richiede il consenso ogni volta che l'app viene eseguita

Per gli utenti di account personali Microsoft, la richiesta di consenso per ogni chiamata di Native Client (desktop o mobile) per autorizzare è il comportamento previsto. L'identità di Native Client è intrinsecamente non sicura, contraria all'identità dell'applicazione client riservata. Le applicazioni client riservate scambiano un segreto con la piattaforma di identità Microsoft per dimostrare la propria identità. La piattaforma di identità Microsoft ha scelto di mitigare questa insicurezza per i servizi consumer richiedendo all'utente il consenso ogni volta che l'applicazione è autorizzata.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
