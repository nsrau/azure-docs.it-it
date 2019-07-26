---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi all'esecuzione di query sulle risorse di Azure con Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480553"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Risolvere gli errori usando il grafico delle risorse di Azure

È possibile che si verifichino errori durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

La maggior parte degli errori è il risultato di un problema durante l'esecuzione di una query con Azure Resource Graph. Quando una query ha esito negativo, l'SDK fornisce informazioni dettagliate sulla query non riuscita. Queste informazioni indicano il problema in modo che sia possibile risolverlo e una query successiva avrà esito positivo.

## <a name="general-errors"></a>Errori generali

### <a name="toomanysubscription"></a>Scenario: Troppe sottoscrizioni

#### <a name="issue"></a>Problema

I clienti che hanno accesso a più di 1000 sottoscrizioni, incluse le sottoscrizioni tra tenant con [Azure Lighthouse](../../../lighthouse/overview.md), non possono recuperare i dati in tutte le sottoscrizioni in una singola chiamata al grafico delle risorse di Azure.

#### <a name="cause"></a>Causa

L'interfaccia della riga di comando di Azure e PowerShell inviano solo le prime 1000 sottoscrizioni di Azure Resource Graph. L'API REST di Azure Resource Graph accetta un numero massimo di sottoscrizioni su cui eseguire la query.

#### <a name="resolution"></a>Risoluzione

Il numero di richieste batch per la query con un subset di sottoscrizioni rimane al di sotto del limite di 1000 sottoscrizioni. La soluzione USA il parametro **Subscription** in PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

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

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.