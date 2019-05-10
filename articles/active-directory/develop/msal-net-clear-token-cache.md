---
title: Cancellare la cache dei token tramite Microsoft Authentication Library per .NET - Azure
description: Informazioni su come cancellare la cache dei token usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604d2833387b105fc7897a89f96ebcf9486d6a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468470"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Cancellare la cache dei token tramite MSAL.NET

Quando si [acquisire un token di accesso](msal-acquire-cache-tokens.md) usando Microsoft Authentication Library per .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione necessita di un token, opportuno chiamare dapprima il `AcquireTokenSilent` metodo per verificare se un token accettabile è presente nella cache. 

La cancellazione della cache viene realizzata mediante gli account di rimozione dalla cache. Questa operazione non rimuove il cookie di sessione che si trova il browser, tuttavia.  Nell'esempio seguente crea un'istanza di un'applicazione client pubblica, ottiene gli account per l'applicazione e rimuove gli account.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Per altre informazioni sull'acquisizione e memorizzazione nella cache i token, leggere [acquisire un token di accesso](msal-acquire-cache-tokens.md).
