---
title: Acquisire un token in modalità invisibile all'utente (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come acquisire un token di accesso in modo invisibile all'utente (dalla cache dei token) usando Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a4c7a96e48ddf5a000888929b8bab719ff89fa
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532689"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Ottenere un token dalla cache dei token usando MSAL.NET

Quando si acquisisce un token di accesso tramite Microsoft Authentication Library per .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione richiede un token, deve prima chiamare il `AcquireTokenSilent` metodo per verificare se un token accettabile si trova nella cache. In molti casi, è possibile acquisire un altro token con più ambiti in base a un token nella cache. È anche possibile aggiornare un token quando si sta ottenendo la scadenza (poiché la cache dei token contiene anche un token di aggiornamento).

Il modello consigliato consiste nel chiamare prima `AcquireTokenSilent` il metodo.  Se `AcquireTokenSilent` ha esito negativo, acquisire un token usando altri metodi.

Nell'esempio seguente l'applicazione tenta prima di acquisire un token dalla cache dei token.  Se viene `MsalUiRequiredException` generata un'eccezione, l'applicazione acquisisce un token in modo interattivo. 

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
