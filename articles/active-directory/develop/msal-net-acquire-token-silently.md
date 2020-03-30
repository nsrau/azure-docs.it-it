---
title: Acquisire un token dalla cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Informazioni su come acquisire un token di accesso in modo invisibile all'utente (dalla cache dei token) usando la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084845"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Ottenere un token dalla cache dei token usando MSAL.NET

Quando si acquisisce un token di accesso utilizzando Microsoft Authentication Library for .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione necessita di un `AcquireTokenSilent` token, deve prima chiamare il metodo per verificare se un token accettabile si trova nella cache. In molti casi, è possibile acquisire un altro token con più ambiti in base a un token nella cache. È anche possibile aggiornare un token quando si sta avvicinando alla scadenza (poiché la cache dei token contiene anche un token di aggiornamento).

Il modello consigliato consiste `AcquireTokenSilent` nel chiamare prima il metodo.  Se `AcquireTokenSilent` ha esito negativo, acquisire un token usando altri metodi.

Nell'esempio seguente, l'applicazione tenta innanzitutto di acquisire un token dalla cache dei token.  Se `MsalUiRequiredException` viene generata un'eccezione, l'applicazione acquisisce un token in modo interattivo. 

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
