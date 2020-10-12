---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi ai vari SDK durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph.
ms.date: 08/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 052c3e1c10f1b0ca94653f086e2df4f3d395070e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056942"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Risolvere errori con Azure Resource Graph

È possibile che si verifichino errori durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

La maggior parte degli errori è il risultato di un problema durante l'esecuzione di una query con Azure Resource Graph. Quando una query ha esito negativo, l'SDK fornisce informazioni dettagliate sulla query non riuscita. Le informazioni indicano il problema in modo da poterlo risolvere e garantire la riuscita di una query successiva.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Scenario: Richieste limitate

#### <a name="issue"></a>Problema

I clienti che eseguono query di risorse grandi o frequenti hanno richieste limitate.

#### <a name="cause"></a>Causa

Azure Resource Graph alloca un numero di quota per ogni utente in base a un intervallo di tempo. Ad esempio, un utente può inviare al massimo 15 query all'interno di ogni intervallo di 5 secondi senza limitazioni. Il valore della quota è determinato da molti fattori ed è soggetto a modifiche. Per altre informazioni, vedere [Limitazione in Azure Resource Graph](../overview.md#throttling).

#### <a name="resolution"></a>Risoluzione

Esistono diversi metodi per gestire le richieste limitate:

- [Raggruppamento di query](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Distribuzione di query](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Query in parallelo](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Paginazione](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenario: troppe sottoscrizioni

#### <a name="issue"></a>Problema

I clienti che hanno accesso a più di 1000 sottoscrizioni, incluse le sottoscrizioni tra tenant con [Azure Lighthouse](../../../lighthouse/overview.md), non possono recuperare dati in tutte le sottoscrizioni con una singola chiamata ad Azure Resource Graph.

#### <a name="cause"></a>Causa

L'interfaccia della riga di comando di Azure e PowerShell trasferiscono solo le prime 1000 sottoscrizioni ad Azure Resource Graph. L'API REST di Azure Resource Graph accetta un numero massimo di sottoscrizioni su cui eseguire la query.

#### <a name="resolution"></a>Risoluzione

Il numero di richieste batch per la query con un subset di sottoscrizioni rimane al di sotto del limite di 1000 sottoscrizioni. La soluzione consiste nell'usare il parametro **Subscription** in PowerShell.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenario: intestazione REST Content-Type non supportata

#### <a name="issue"></a>Problema

I clienti che eseguono query sull'API REST di Azure Resource Graph ottengono una risposta _500_ (errore interno del server).

#### <a name="cause"></a>Causa

L'API REST di Azure Resource Graph supporta solo un tipo `Content-Type` **application/json**. Per impostazione predefinita, alcuni strumenti o agenti REST sono impostati su **text/plain**, opzione non supportata dall'API REST.

#### <a name="resolution"></a>Risoluzione

Verificare che nello strumento o nell'agente usato per eseguire query su Azure Resource Graph l'intestazione API REST `Content-Type` sia configurata per **application/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenario: nessuna autorizzazione di lettura per tutte le sottoscrizioni nell'elenco

#### <a name="issue"></a>Problema

I clienti che passano esplicitamente un elenco di sottoscrizioni con una query di Azure Resource Graph ottengono una risposta _403_ (accesso negato).

#### <a name="cause"></a>Causa

Se il cliente non dispone dell'autorizzazione di lettura per tutte le sottoscrizioni fornite, la richiesta viene negata a causa della mancanza di diritti di sicurezza appropriati.

#### <a name="resolution"></a>Risoluzione

Includere almeno una sottoscrizione nell'elenco di sottoscrizioni per cui il cliente che esegue la query disponga almeno dell'accesso in lettura. Per altre informazioni, vedere [Autorizzazioni in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.