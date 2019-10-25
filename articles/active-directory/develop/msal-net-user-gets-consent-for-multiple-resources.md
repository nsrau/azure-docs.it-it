---
title: Ottenere il consenso per diverse risorse (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: c3a3d50e94f76341dc349eda997d6e25ca96dec0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802697"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>L'utente ottiene il consenso per diverse risorse usando MSAL.NET
L'endpoint della piattaforma di identità Microsoft non consente di ottenere un token per più risorse contemporaneamente. Quando si usa Microsoft Authentication Library per .NET (MSAL.NET), il parametro Scopes nel metodo di acquisizione del token deve contenere solo ambiti per una singola risorsa. È tuttavia possibile pre-concedere il consenso a più risorse in anticipo specificando gli ambiti aggiuntivi tramite il metodo del generatore `.WithExtraScopeToConsent`.

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma di identità Microsoft, ma non per Azure AD B2C. Azure AD B2C supporta solo il consenso dell'amministratore, non il consenso dell'utente.

Ad esempio, se si dispone di due risorse con due ambiti ciascuno:

- https:\//mytenant.onmicrosoft.com/customerapi (con 2 ambiti `customer.read` e `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (con 2 ambiti `vendor.read` e `vendor.write`)

Usare il modificatore `.WithExtraScopeToConsent` con il parametro *extraScopesToConsent* , come illustrato nell'esempio seguente:

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
