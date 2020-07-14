---
title: "Avvio rapido: Prima query con l'API REST"
description: Questo argomento di avvio rapido illustra la procedura per chiamare l'endpoint Resource Graph per l'API REST ed eseguire la prima query.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: 8776a107484691ffab72f2e1622ed5837375b7fb
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802617"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Avvio rapido: Eseguire la prima query di Resource Graph con l'API REST

Il primo passaggio da eseguire per usare Azure Resource Graph con l'API REST consiste nel verificare che sia disponibile uno strumento per chiamare le API REST. Questo argomento di avvio rapido illustra il processo di esecuzione di una query e di recupero dei risultati mediante la chiamata dell'endpoint dell'API REST di Azure Resource Graph.

Al termine della procedura, gli strumenti per chiamare gli endpoint dell'API REST saranno disponibili e sarà possibile eseguire la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Introduzione all'API REST

Se non si ha familiarità con l'API REST, iniziare consultando le [informazioni di riferimento sull'API REST di Azure](/rest/api/azure/) per acquisire una comprensione generale dell'API REST, in particolare dell'URI della richiesta e del corpo della richiesta. Questo articolo usa questi concetti per fornire indicazioni sull'uso di Azure Resource Graph e ne presuppone una certa conoscenza. Strumenti come [ARMClient](https://github.com/projectkudu/ARMClient) e altri possono gestire automaticamente l'autorizzazione e sono consigliati per gli utenti meno esperti.

Per le specifiche di Azure Resource Graph, vedere [API REST di Azure Resource Graph](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se non si ha già uno strumento per effettuare chiamate API REST, provare a usare PowerShell per queste istruzioni. Il codice di esempio seguente consente di recuperare un'intestazione per l'autenticazione con Azure. Generare un'intestazione di autenticazione, talvolta denominata **bearer token**e fornire l'URI dell'API REST cui connettersi con qualsiasi parametro o un **corpo della richiesta**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Sostituire `{subscriptionId}` nella variabile **$restUri** precedente per ottenere informazioni sulla sottoscrizione. La variabile $response contiene il risultato del cmdlet `Invoke-RestMethod`, che può essere analizzato con cmdlet come [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se l'endpoint servizio API REST si aspetta un **corpo della richiesta**, fornire una variabile in formato JSON per il parametro `-Body` di `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Dopo avere aggiunto gli strumenti dell'API REST all'ambiente scelto, provare a eseguire una semplice query di Resource Graph. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

Nel corpo della richiesta di ogni chiamata all'API REST è presente una variabile che è necessario sostituire con il valore personalizzato:

- `{subscriptionID}`: sostituire con l'ID sottoscrizione

1. Eseguire la prima query di Azure Resource Graph usando l'API REST e l'endpoint `resources`:

   - URI DELL'API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Request Body

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
         }
     }
     ```

   > [!NOTE]
   > Poiché questo esempio di query non prevede un modificatore del tipo di ordinamento, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Aggiornare la chiamata all'endpoint `resouces` e modificare la **query** per applicare `order by` alla proprietà **Name**:

   - URI DELL'API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Request Body

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
         }
     }
     ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Aggiornare la chiamata a `resources` e modificare la **query** per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   - URI DELL'API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Request Body

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
         }
     }
     ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

Per altri esempi di chiamate ad API REST per Azure Resource Graph, vedere gli [esempi REST di Azure Resource Graph](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Pulire le risorse

L'API REST non prevede librerie o moduli da disinstallare. Se per effettuare le chiamate è stato installato uno strumento come _ARMClient_ o _Postman_ che non è più necessario, è possibile disinstallarlo.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato chiamato l'endpoint dell'API REST di Resource Graph ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)
