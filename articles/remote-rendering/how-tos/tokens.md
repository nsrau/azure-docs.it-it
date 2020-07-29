---
title: Ottenere i token di accesso al servizio
description: Viene descritto come creare i token per l'accesso alle API REST ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687078"
---
# <a name="get-service-access-tokens"></a>Ottenere i token di accesso al servizio

L'accesso alle API REST ARR viene concesso solo agli utenti autorizzati. Per dimostrare l'autorizzazione, è necessario inviare un *token di accesso* insieme alle richieste REST. Questi token vengono rilasciati dal *servizio token di sicurezza* (STS) in Exchange per una chiave dell'account. I token hanno una **durata di 24 ore** e possono quindi essere rilasciati agli utenti senza concedere loro l'accesso completo al servizio.

Questo articolo descrive come creare un token di accesso di questo tipo.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account arr](create-an-account.md), se non ne è già presente uno.

## <a name="token-service-rest-api"></a>API REST del servizio token

Per creare i token di accesso, il *servizio token di sicurezza* fornisce una singola API REST. L'URL del servizio STS ARR è https: \/ /STS.mixedreality.Azure.com.

### <a name="get-token-request"></a>Richiesta ' Get token '

| URI | Metodo |
|-----------|:-----------|
| /Accounts/**accountId**/token | GET |

| Intestazione | valore |
|--------|:------|
| Autorizzazione | "Bearer **accountId**:**AccountKey**" |

Sostituire *accountId* e *AccountKey* con i rispettivi dati.

### <a name="get-token-response"></a>Risposta ' Get token '

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | AccessToken: stringa | Operazione completata |

| Header | Scopo |
|--------|:------|
| MS-CV | Questo valore può essere utilizzato per tracciare la chiamata all'interno del servizio |

## <a name="getting-a-token-using-powershell"></a>Ottenere un token tramite PowerShell

Il codice di PowerShell riportato di seguito illustra come inviare la richiesta REST necessaria al servizio token di servizio. Il token viene quindi stampato nel prompt di PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Lo script stampa semplicemente il token nell'output, da cui è possibile copiare & incollarlo. Per un progetto reale, è consigliabile automatizzare questo processo.

## <a name="next-steps"></a>Passaggi successivi

* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
* [API front-end di Azure](../how-tos/frontend-apis.md)
* [API REST di gestione delle sessioni](../how-tos/session-rest-api.md)
