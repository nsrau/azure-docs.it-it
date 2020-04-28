---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi ai vari SDK durante l'esecuzione di query sulle risorse di Azure con il grafico delle risorse di Azure.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74303900"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Risolvere gli errori usando il grafico delle risorse di Azure

È possibile che si verifichino errori durante l'esecuzione di query sulle risorse di Azure con Azure Resource Graph. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

La maggior parte degli errori è il risultato di un problema durante l'esecuzione di una query con Azure Resource Graph. Quando una query ha esito negativo, l'SDK fornisce informazioni dettagliate sulla query non riuscita. Queste informazioni indicano il problema in modo che sia possibile risolverlo e una query successiva avrà esito positivo.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenario: troppe sottoscrizioni

#### <a name="issue"></a>Problema

I clienti che hanno accesso a più di 1000 sottoscrizioni, incluse le sottoscrizioni tra tenant con [Azure Lighthouse](../../../lighthouse/overview.md), non possono recuperare i dati in tutte le sottoscrizioni in una singola chiamata al grafico delle risorse di Azure.

#### <a name="cause"></a>Causa

L'interfaccia della riga di comando di Azure e PowerShell inviano solo le prime 1000 sottoscrizioni di Azure Resource Graph. L'API REST di Azure Resource Graph accetta un numero massimo di sottoscrizioni su cui eseguire la query.

#### <a name="resolution"></a>Risoluzione

Il numero di richieste batch per la query con un subset di sottoscrizioni rimane al di sotto del limite di 1000 sottoscrizioni. La soluzione USA il parametro **Subscription** in PowerShell.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenario: intestazione REST del tipo di contenuto non supportata

#### <a name="issue"></a>Problema

I clienti che eseguono query sull'API REST di Azure Resource Graph ottengono una risposta _500_ (errore interno del server) restituita.

#### <a name="cause"></a>Causa

L'API REST di Azure Resource Graph supporta solo `Content-Type` una di **Application/JSON**. Per impostazione predefinita, alcuni strumenti REST o gli agenti sono di **testo/normale**, che non sono supportati dall'API REST.

#### <a name="resolution"></a>Risoluzione

Verificare che lo strumento o l'agente che si sta usando per eseguire query in Azure Resource Graph disponga `Content-Type` dell'intestazione API REST configurata per **Application/JSON**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenario: nessuna autorizzazione di lettura per tutte le sottoscrizioni nell'elenco

#### <a name="issue"></a>Problema

I clienti che passano esplicitamente un elenco di sottoscrizioni con una query di Azure Resource Graph ottengono una risposta _403_ (accesso negato).

#### <a name="cause"></a>Causa

Se il cliente non dispone dell'autorizzazione di lettura per tutte le sottoscrizioni fornite, la richiesta viene negata a causa della mancanza di diritti di protezione appropriati.

#### <a name="resolution"></a>Risoluzione

Includere almeno una sottoscrizione nell'elenco di sottoscrizioni per cui il cliente che esegue la query dispone almeno dell'accesso in lettura a. Per altre informazioni, vedere [autorizzazioni in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
- Connetti con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.