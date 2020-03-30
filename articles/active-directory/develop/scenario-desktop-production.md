---
title: Spostare l'app desktop che chiama le API Web nell'ambiente di produzione - Piattaforma di identità Microsoft Azure
description: Informazioni su come spostare un'app desktop che chiama le API Web nell'ambiente di produzione
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262568"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>App desktop che chiama le API Web: passa all'ambiente di produzione

In questo articolo imparerai a spostare l'app desktop che chiama le API Web nell'ambiente di produzione.

## <a name="handle-errors-in-desktop-applications"></a>Gestire gli errori nelle applicazioni desktop

Nei diversi flussi è stato illustrato come gestire gli errori per i flussi silenziosi, come illustrato nei frammenti di codice. Hai anche visto che ci sono casi in cui è necessaria l'interazione, come nel consenso incrementale e nell'accesso condizionale.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Chiedere all'utente di ottenere il consenso in anticipo per diverse risorse

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure Active Directory (Azure AD) B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Non è possibile ottenere un token per più risorse contemporaneamente con l'endpoint Microsoft Identity Platform (v2.0). Il `scopes` parametro può contenere ambiti per una sola risorsa. È possibile assicurarsi che l'utente preconda `extraScopesToConsent` diverse risorse utilizzando il parametro .

Ad esempio, si potrebbero avere due risorse che hanno due ambiti ciascuno:For instance, you might have two resources that have two scopes each:

- `https://mytenant.onmicrosoft.com/customerapi`con gli `customer.read` ambiti e`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`con gli `vendor.read` ambiti e`vendor.write`

In questo esempio, `.WithAdditionalPromptToConsent` utilizzare il `extraScopesToConsent` modificatore che contiene il parametro .

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

Questa chiamata consente di ottenere un token di accesso per la prima API Web.This call gets you an access token for the first web API.

Quando è necessario chiamare la seconda `AcquireTokenSilent` API Web, chiamare l'API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>L'account personale Microsoft richiede il reprevicè ogni volta che viene eseguita l'app

Per gli utenti di account personali Microsoft, chiedere nuovamente il consenso su ogni chiamata di client nativo (desktop o app per dispositivi mobili) per autorizzare è il comportamento previsto. L'identità del client nativo è intrinsecamente insicura, il che è contrario all'identità riservata dell'applicazione client. Le applicazioni client riservate si scambiano un segreto con la piattaforma Microsoft Identity per dimostrare la propria identità. La piattaforma di identità Microsoft ha scelto di ridurre questa insicurezza per i servizi consumer richiedendo all'utente il consenso ogni volta che l'applicazione viene autorizzata.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
