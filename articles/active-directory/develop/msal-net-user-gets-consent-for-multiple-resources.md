---
title: Ottenere il consenso per diverse risorse (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come un utente può ottenere il consenso preliminare per diverse risorse tramite Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.openlocfilehash: 4ded7a6fc465b4cfc98d26f65195f89de8381ac6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532362"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>L'utente ottiene il consenso per diverse risorse usando MSAL.NET
L'endpoint della piattaforma di identità Microsoft non consente di ottenere un token per più risorse contemporaneamente. Quando si usa Microsoft Authentication Library per .NET (MSAL.NET), il parametro Scopes nel metodo di acquisizione del token deve contenere solo ambiti per una singola risorsa. Tuttavia, è possibile pre-concedere il consenso a più risorse in anticipo specificando gli ambiti aggiuntivi tramite il metodo del `.WithExtraScopeToConsent` generatore.

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure AD B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Ad esempio, se si dispone di due risorse con due ambiti ciascuno:

- https:\//mytenant.onmicrosoft.com/customerapi (con 2 `customer.read` ambiti e `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (con 2 `vendor.read` ambiti e `vendor.write`)

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
