---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi con i vari SDK durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph.Learn how to troubleshoot issues with the various SdkKs while querying Azure resources with Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303900"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Risolvere gli errori usando Azure Resource GraphTroubleshoot errors using Azure Resource Graph

È possibile che si verifichino errori durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph.You may run into errors when querying Azure resources with Azure Resource Graph. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

La maggior parte degli errori sono il risultato di un problema durante l'esecuzione di una query con Azure Resource Graph.Most errors are the result of an issue while running a query with Azure Resource Graph. Quando una query ha esito negativo, l'SDK fornisce dettagli sulla query non riuscita. Queste informazioni indicano il problema in modo che possa essere risolto e una query successiva ha esito positivo.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenario: troppe sottoscrizioniScenario: Too many subscriptions

#### <a name="issue"></a>Problema

I clienti con accesso a più di 1000 sottoscrizioni, incluse le sottoscrizioni cross-tenant con [Azure Lighthouse,](../../../lighthouse/overview.md)non possono recuperare dati in tutte le sottoscrizioni in una singola chiamata ad Azure Resource Graph.

#### <a name="cause"></a>Causa

Azure CLI and PowerShell forward only the first 1000 subscriptions to Azure Resource Graph. L'API REST per Azure Resource Graph accetta un numero massimo di sottoscrizioni su cui eseguire la query.

#### <a name="resolution"></a>Risoluzione

Le richieste batch per la query con un sottoinsieme di sottoscrizioni rimangono al di sotto del limite di 1000 sottoscrizioni. La soluzione usa il parametro Subscription in PowerShell.The solution is using the **Subscription** parameter in PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenario: intestazione REST Content-Type non supportataScenario: Unsupported Content-Type REST header

#### <a name="issue"></a>Problema

I clienti che eseguono query sull'API REST di Azure Resource Graph ricevono una risposta _500_ (Errore interno del server) restituita.

#### <a name="cause"></a>Causa

L'API REST di Azure `Content-Type` Resource Graph supporta solo **un'api application/json**. Alcuni strumenti o agenti REST per impostazione predefinita **sono text/plain**, che non è supportato dall'API REST.

#### <a name="resolution"></a>Risoluzione

Verificare che lo strumento o l'agente usato per eseguire `Content-Type` query su Azure Resource Graph abbia l'intestazione dell'API REST configurata per **application/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenario: nessuna autorizzazione di lettura per tutte le sottoscrizioni nell'elencoScenario: No read permission to all subscriptions in list

#### <a name="issue"></a>Problema

I clienti che passano in modo esplicito un elenco di sottoscrizioni con una query di Azure Resource Graph ottengono una risposta _403_ (Accesso negato).

#### <a name="cause"></a>Causa

Se il cliente non dispone dell'autorizzazione di lettura per tutte le sottoscrizioni fornite, la richiesta viene negata a causa della mancanza di diritti di sicurezza appropriati.

#### <a name="resolution"></a>Risoluzione

Includere almeno una sottoscrizione nell'elenco delle sottoscrizioni a cui il cliente che esegue la query ha almeno l'accesso in lettura. Per altre informazioni, vedere Autorizzazioni in Azure Resource Graph.For more information, see [Permissions in Azure Resource Graph.](../overview.md#permissions-in-azure-resource-graph)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
- Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.