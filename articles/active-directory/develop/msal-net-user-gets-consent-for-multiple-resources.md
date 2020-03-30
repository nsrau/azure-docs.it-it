---
title: Ottenere il consenso per diverse risorse (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come un utente può ottenere il preconsenso per diverse risorse utilizzando la libreria di autenticazione Microsoft per .NET (MSAL.NET).
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
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085833"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>L'utente ottiene il consenso per diverse risorse utilizzando MSAL.NET
L'endpoint della piattaforma di identità Microsoft non consente di ottenere un token per più risorse contemporaneamente. Quando si usa la libreria di autenticazione Microsoft per .NET (MSAL.NET), il parametro scopes nel metodo del token acquire deve contenere solo ambiti per una singola risorsa. Tuttavia, è possibile pre-acconsentire a diverse risorse `.WithExtraScopeToConsent` in anticipo specificando ambiti aggiuntivi utilizzando il metodo builder.

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure AD B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Ad esempio, se si dispone di due risorse con due ambiti ciascuno:For example, if you have two resources that have 2 scopes each:

- https:\//mytenant.onmicrosoft.com/customerapi (con `customer.read` 2 `customer.write`ambiti e )
- https:\//mytenant.onmicrosoft.com/vendorapi (con `vendor.read` 2 `vendor.write`ambiti e )

È necessario `.WithExtraScopeToConsent` utilizzare il modificatore che dispone del parametro *extraScopesToConsent,* come illustrato nell'esempio seguente:

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

In questo modo si otterrà un token di accesso per la prima API Web. Quindi, quando è necessario accedere alla seconda API Web è possibile acquisire automaticamente il token dalla cache dei token:Then, when you need to access the second web API you can silently acquire the token from the token cache:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
