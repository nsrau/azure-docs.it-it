---
title: Eseguire la prima query con Azure PowerShell
description: Questo articolo illustra la procedura per abilitare il modulo di Resource Graph per Azure PowerShell ed eseguire la prima query.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6c58528aba29cbbc81694112815534a8fc525b8a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565385"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure PowerShell

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che il modulo per Azure PowerShell sia installato. Questa guida introduttiva illustra il processo di aggiunta del modulo all'installazione di Azure PowerShell.

Alla fine di questo processo, il modulo risulterà aggiunto all'installazione di Azure PowerShell scelta e si eseguirà la prima query di Resource Graph.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Aggiungere il modulo di Resource Graph

Per consentire ad Azure PowerShell di eseguire query su Azure Resource Graph, il modulo deve essere aggiunto. Questo modulo può essere usato con PowerShell installato in locale, con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisiti di base

Il modulo Azure Resource Graph richiede il software seguente:

- Azure PowerShell 1.0.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 o versione successiva. Se non è installato o aggiornato, seguire [queste istruzioni](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installare il modulo

Il modulo Resource Graph per PowerShell è **Az.ResourceGraph**.

1. Da un prompt di PowerShell **amministrativo** eseguire i comandi seguenti:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Verificare che il modulo sia stato importato e che la versione sia corretta (0.7.3):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Ora che il modulo di Azure PowerShell è stato aggiunto all'ambiente scelto, è possibile provare a eseguire una semplice query di Resource Graph. La query restituirà le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

1. Eseguire la prima query di Azure Resource Graph usando il cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Aggiornare la query applicando `order by` alla proprietà **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. In questo modo i risultati della query verranno prima limitati e poi ordinati.

1. Aggiornare la query applicando prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e, come previsto, ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

> [!NOTE]
> Se la query non restituisce risultati da una sottoscrizione a cui si ha già accesso, tenere presente che il cmdlet `Search-AzGraph` è impostato sulle sottoscrizioni nel contesto predefinito. Per visualizzare l'elenco di ID di sottoscrizione che fanno parte del contesto predefinito, eseguire `(Get-AzContext).Account.ExtendedProperties.Subscriptions`. Per eseguire la ricerca in tutte le sottoscrizioni a cui si ha accesso, è possibile impostare PSDefaultParameterValues per il cmdlet `Search-AzGraph` eseguendo `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il modulo di Resource Graph dall'ambiente di Azure PowerShell, è possibile usare il comando seguente:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Questo comando non elimina il file del modulo scaricato in precedenza, ma lo rimuove semplicemente dalla sessione di PowerShell in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere altre informazioni sul [linguaggio di query](./concepts/query-language.md)
- Informazioni su come [esplorare le risorse](./concepts/explore-resources.md)
- Eseguire la prima query con l'[interfaccia della riga di comando di Azure](first-query-azurecli.md)
- Vedere esempi di [query di base](./samples/starter.md)
- Vedere esempi di [query avanzate](./samples/advanced.md)
- Commenti e suggerimenti su [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)
