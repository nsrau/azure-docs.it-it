---
title: Eseguire la prima query di Resource Graph usando Azure PowerShell
description: Questo articolo illustra la procedura per abilitare il modulo di Resource Graph per Azure PowerShell ed eseguire la prima query.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7a706c65fbdd64103854b02e891c96cbf927f8a1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962539"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Eseguire la prima query di Resource Graph usando Azure PowerShell

Il primo passaggio per usare Azure Resource Graph richiede di assicurarsi che il modulo per Azure PowerShell sia installato. Questa guida introduttiva illustra il processo di aggiunta del modulo all'installazione di Azure PowerShell. È possibile usare il modulo con Azure PowerShell installato in locale o tramite [Azure Cloud Shell](https://shell.azure.com).

Alla fine di questo processo, il modulo risulterà aggiunto all'installazione di Azure PowerShell scelta e si eseguirà la prima query di Resource Graph.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="add-the-resource-graph-module"></a>Aggiungere il modulo di Resource Graph

Per consentire ad Azure PowerShell di eseguire query su Azure Resource Graph, il modulo deve essere aggiunto. Questo modulo funziona ovunque sia possibile usare Azure PowerShell, inclusi [Cloud Shell](https://shell.azure.com) (sia autonomo che nel portale) e l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/), o installato in locale.

1. Assicurarsi che sia installato Azure PowerShell 6.3.0 o versioni successive. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-azurerm-ps).

1. Assicurarsi che PowerShellGet sia installato. Se non è installato o aggiornato, seguire [queste istruzioni](/powershell/gallery/installing-psget).

1. Da un prompt di PowerShell **amministrativo** eseguire i comandi seguenti:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module AzureRm.ResourceGraph
   ```

1. Verificare che il modulo sia stato importato e che la versione sia corretta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported AzureRm.Graph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Ora che il modulo di Azure PowerShell è stato aggiunto all'ambiente scelto, è possibile provare a eseguire una semplice query di Resource Graph. La query restituirà le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

1. Eseguire la prima query di Azure Resource Graph usando il cmdlet `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Aggiornare la query applicando `order by` alla proprietà **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. In questo modo i risultati della query verranno prima limitati e poi ordinati.

1. Aggiornare la query applicando prima `order by` alla proprietà **Name** e quindi `limit` ai primi 5 risultati:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e, come previsto, ordinati in base alla proprietà **Name**, ma ancora limitati ai primi 5 risultati.

## <a name="clean-up"></a>Eliminazione

Per rimuovere il modulo di Resource Graph dall'ambiente di Azure PowerShell, è possibile usare il comando seguente:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Questo comando non elimina il file del modulo scaricato in precedenza, ma lo rimuove semplicemente dalla sessione di PowerShell in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere altre informazioni sul [linguaggio di query](./concepts/query-language.md)
- Informazioni su come [esplorare le risorse](./concepts/explore-resources.md)
- Eseguire la prima query con l'[interfaccia della riga di comando di Azure](first-query-azurecli.md)
- Vedere esempi di [query di base](./samples/starter.md)
- Vedere esempi di [query avanzate](./samples/advanced.md)