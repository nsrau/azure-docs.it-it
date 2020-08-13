---
title: Acquisire un token dalla cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Informazioni su come acquisire un token di accesso in modo invisibile all'utente (dalla cache dei token) usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 95cfb937ce236d0acd1a3369068afc6f3b505aed
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166247"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Ottenere un token dalla cache dei token usando MSAL.NET

Quando si acquisisce un token di accesso tramite Microsoft Authentication Library per .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione richiede un token, deve prima chiamare il `AcquireTokenSilent` metodo per verificare se un token accettabile si trova nella cache. In molti casi, è possibile acquisire un altro token con più ambiti in base a un token nella cache. È anche possibile aggiornare un token quando si sta ottenendo la scadenza (poiché la cache dei token contiene anche un token di aggiornamento).

Il modello consigliato consiste nel chiamare `AcquireTokenSilent` prima il metodo.  Se ha `AcquireTokenSilent` esito negativo, acquisire un token usando altri metodi.

Nell'esempio seguente l'applicazione tenta prima di acquisire un token dalla cache dei token.  Se `MsalUiRequiredException` viene generata un'eccezione, l'applicazione acquisisce un token in modo interattivo. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
