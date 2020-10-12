---
title: Ottenere il consenso per diverse risorse (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come un utente può ottenere il consenso preliminare per diverse risorse tramite Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6333d935e1a902ba173017f8149c098f44398955
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165873"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>L'utente ottiene il consenso per diverse risorse usando MSAL.NET
L'endpoint della piattaforma di identità Microsoft non consente di ottenere un token per più risorse contemporaneamente. Quando si usa Microsoft Authentication Library per .NET (MSAL.NET), il parametro Scopes nel metodo di acquisizione del token deve contenere solo ambiti per una singola risorsa. Tuttavia, è possibile pre-concedere il consenso a più risorse in anticipo specificando gli ambiti aggiuntivi tramite il `.WithExtraScopeToConsent` metodo del generatore.

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure AD B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Ad esempio, se si dispone di due risorse con due ambiti ciascuno:

- https: \/ /mytenant.onmicrosoft.com/customerapi (con 2 ambiti `customer.read` e `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (con 2 ambiti `vendor.read` e `vendor.write` )

Usare il `.WithExtraScopeToConsent` modificatore con il parametro *extraScopesToConsent* , come illustrato nell'esempio seguente:

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

In questo modo si otterrà un token di accesso per la prima API Web. Quindi, quando è necessario accedere alla seconda API Web, è possibile acquisire automaticamente il token dalla cache dei token:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
