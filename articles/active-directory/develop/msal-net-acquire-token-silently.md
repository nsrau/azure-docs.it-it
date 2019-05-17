---
title: Acquisire un token in modo invisibile (Microsoft Authentication Library per .NET) | Azure
description: Informazioni su come acquisire un token di accesso in modo automatico (dalla cache dei token) con Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544231"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Ottenere un token dalla cache dei token tramite MSAL.NET

Quando si acquisisce un token di accesso usando Microsoft Authentication Library per .NET (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione necessita di un token, opportuno chiamare dapprima il `AcquireTokenSilent` metodo per verificare se un token accettabile è presente nella cache. In molti casi, è possibile acquisire un altro token con altri ambiti in base un token nella cache. È anche possibile aggiornare un token quando si ottiene sta per scadere (perché la cache dei token contiene anche un token di aggiornamento).

Il modello consigliato consiste nel chiamare il `AcquireTokenSilent` metodo prima.  Se `AcquireTokenSilent` ha esito negativo, quindi acquisire un token utilizzando altri metodi.

Nell'esempio seguente, l'applicazione prima di tutto prova ad acquisire un token dalla cache dei token.  Se un `MsalUiRequiredException` eccezione, l'applicazione acquisisce un token in modo interattivo. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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