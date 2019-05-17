---
title: Ottenere il consenso per varie risorse (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come un utente può ottenere il consenso non definitiva per diverse risorse usando Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.openlocfilehash: 8f7d24a1e14cfbb1163ab78b94dd36ec288dce50
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544057"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Ottiene il consenso per varie risorse tramite MSAL.NET
L'endpoint di piattaforma di identità di Microsoft non consente di ottenere un token per diverse risorse in una sola volta. Quando si usa Microsoft Authentication Library per .NET (MSAL.NET), il parametro gli ambiti nel metodo di token di acquisizione deve contenere solo gli ambiti per una singola risorsa. Tuttavia, è possibile pre-fornire il consenso a numerose risorse upfront specificando altri ambiti tramite il `.WithExtraScopeToConsent` metodo del generatore.

> [!NOTE]
> Ottenere il consenso per diverse risorse funziona per la piattaforma delle identità Microsoft, ma non per Azure AD B2C. Azure AD B2C supporta solo il di consenso dell'amministratore, non il consenso dell'utente.

Ad esempio, se si dispongono di due risorse con gli ambiti 2 ognuna:

- https://mytenant.onmicrosoft.com/customerapi (con 2 ambiti `customer.read` e `customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (con 2 ambiti `vendor.read` e `vendor.write`)

È consigliabile usare la `.WithExtraScopeToConsent` modificatore con il *extraScopesToConsent* parametro come illustrato nell'esempio seguente:

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

Per ottenere un token di accesso per la prima API web. Quindi, quando è necessario accedere all'API web secondo invisibile all'utente è possibile acquisire il token dalla cache dei token:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
