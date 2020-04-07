---
title: Ottenere token di accesso al servizioGet service access tokens
description: Descrive come creare token per l'accesso alle API REST DI ARRDescribes how to create tokens for accessing the ARR REST APIs
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681194"
---
# <a name="get-service-access-tokens"></a>Ottenere token di accesso al servizioGet service access tokens

L'accesso alle API REST ARR viene concesso solo per gli utenti autorizzati. Per dimostrare l'autorizzazione, è necessario inviare un token di *accesso* insieme alle richieste REST. Questi token vengono emessi dal *servizio* token di sicurezza in cambio di una chiave dell'account. I token hanno una **durata di 24 ore** e quindi possono essere rilasciati agli utenti senza fornire loro l'accesso completo al servizio.

In questo articolo viene descritto come creare tale token di accesso.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account ARR,](create-an-account.md)se non ne hai ancora uno.

## <a name="token-service-rest-api"></a>API REST del servizio tokenToken service REST API

Per creare token di accesso, il *servizio token di* sicurezza fornisce una singola API REST. L'URL per il servizio [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com)servizio token di sicurezza ARR è .

### <a name="get-token-request"></a>Richiesta 'Get token'

| URI | Metodo |
|-----------|:-----------|
| /accounts/**idaccount /token** | GET |

| Intestazione | valore |
|--------|:------|
| Autorizzazione | **"Bearer accountId**:**accountKey"** |

Sostituire *accountId* e *accountKey* con i rispettivi dati.

### <a name="get-token-response"></a>Risposta 'Get token'

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | AccessToken: stringa | Operazione completata |

| Intestazione | Scopo |
|--------|:------|
| MS-CV | Questo valore può essere utilizzato per tracciare la chiamata all'interno del servizio |

## <a name="getting-a-token-using-powershell"></a>Ottenere un token tramite PowerShellGetting a token using PowerShell

Il codice Di PowerShell riportato di seguito illustra come inviare la richiesta REST necessaria al servizio token di sicurezza. Il token viene quindi stampato nel prompt di PowerShell.It then prints the token to the PowerShell prompt.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Lo script stampa solo il token nell'output, da dove è possibile copiare & incollarlo. Per un progetto reale, è necessario automatizzare questo processo.

## <a name="next-steps"></a>Passaggi successivi

* [Script di PowerShell di esempioExample PowerShell scripts](../samples/powershell-example-scripts.md)
* [API front-end di AzureAzure Frontend APIs](../how-tos/frontend-apis.md)
* [API REST di gestione delle sessioni](../how-tos/session-rest-api.md)
