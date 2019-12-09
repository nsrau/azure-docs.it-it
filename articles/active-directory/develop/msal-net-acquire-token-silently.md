---
title: Acquisire un token in modo invisibile all'utente (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Informazioni su come acquisire un token di accesso in modo invisibile all'utente (dalla cache dei token) usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c988eba278e184d0e60a4ede456a340c258e71
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915979"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Ottenere un token dalla cache dei token usando MSAL.NET

Quando si acquisisce un token di accesso tramite Microsoft Authentication Library per .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione richiede un token, deve prima chiamare il metodo `AcquireTokenSilent` per verificare se un token accettabile si trova nella cache. In molti casi, è possibile acquisire un altro token con più ambiti in base a un token nella cache. È anche possibile aggiornare un token quando si sta ottenendo la scadenza (poiché la cache dei token contiene anche un token di aggiornamento).

Il modello consigliato consiste nel chiamare prima il metodo `AcquireTokenSilent`.  Se `AcquireTokenSilent` ha esito negativo, acquisire un token usando altri metodi.

Nell'esempio seguente l'applicazione tenta prima di acquisire un token dalla cache dei token.  Se viene generata un'eccezione `MsalUiRequiredException`, l'applicazione acquisisce un token in modo interattivo. 

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
