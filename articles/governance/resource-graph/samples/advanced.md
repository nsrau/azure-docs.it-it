---
title: Esempi di query avanzate
description: Usare Azure Resource Graph per eseguire alcune query avanzate, ad esempio per ottenere la capacità di un set di scalabilità di macchine virtuali, elencare tutti i tag usati e cercare la corrispondenza di macchine virtuali con espressioni regolari.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 33c67f77a26e2a4fc97d7f5483aad53c121e117b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70239009"
---
# <a name="advanced-resource-graph-queries"></a>Query avanzate di Resource Graph

Il primo passaggio per capire le query con Azure Resource Graph è conoscere i fondamenti del [linguaggio di query](../concepts/query-language.md). Se non si ha familiarità con [Esplora dati di Azure](../../../data-explorer/data-explorer-overview.md), è consigliabile impararne le nozioni di base per capire come comporre richieste in grado di individuare le risorse desiderate.

Si esamineranno le query avanzate seguenti:

> [!div class="checklist"]
> - [Ottenere la capacità e le dimensioni di un set di scalabilità di macchine virtuali](#vmss-capacity)
> - [Elencare tutti i nomi di tag](#list-all-tags)
> - [Macchine virtuali individuate da un'espressione regolare](#vm-regex)
> - [Includere i nomi di tenant e sottoscrizioni con DisplayNames](#displaynames)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="language-support"></a>Supporto per le lingue

Azure Resource Graph è supportato dall'interfaccia della riga di comando di Azure tramite un'estensione e da Azure PowerShell tramite un modulo. Prima di eseguire le query seguenti, verificare che l'ambiente sia pronto. Per i passaggi necessari per installare e convalidare l'ambiente shell preferito, vedere le informazioni relative all'[interfaccia della riga di comando di Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e ad [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="vmss-capacity"></a>Ottenere la capacità e le dimensioni di un set di scalabilità di macchine virtuali

Questa query cerca risorse di set di scalabilità di macchine virtuali e ottiene vari dettagli fra cui le dimensioni della macchina virtuale e la capacità del set di scalabilità. Questa query usa la funzione `toint()` per eseguire il cast della capacità in un numero, in modo che possa essere ordinato. Infine, le colonne vengono rinominate in proprietà denominate personalizzate.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Elencare tutti i nomi di tag

Questa query inizia con il tag e compila un oggetto JSON che elenca tutti i nomi di tag univoci e i tipi corrispondenti.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Macchine virtuali individuate da un'espressione regolare

Questa query cerca le macchine virtuali che corrispondono a un'[espressione regolare](/dotnet/standard/base-types/regular-expression-language-quick-reference) (nota come _regex_). **matches regex \@** permette di definire l'espressione regolare usata per cercare le corrispondenze, ovvero `^Contoso(.*)[0-9]+$`.
Tale definizione di espressione regolare è spiegata come:

- `^` - La corrispondenza deve cominciare all'inizio della stringa.
- `Contoso` - Stringa con distinzione tra maiuscole e minuscole.
- `(.*)` - Una corrispondenza di espressione secondaria:
  - `.` - Trova la corrispondenza di qualsiasi carattere, ad eccezione del carattere nuova riga.
  - `*` - Trova la corrispondenza dell'elemento precedente zero o più volte.
- `[0-9]` - Corrispondenza del gruppo di caratteri per i numeri da 0 a 9.
- `+` - Trova la corrispondenza dell'elemento precedente una o più volte.
- `$` - La corrispondenza dell'elemento precedente deve essere presente alla fine della stringa.

Dopo aver cercato le corrispondenze in base al nome, la query proietta il nome e applica l'ordinamento in base al nome in modo crescente.

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>Includere i nomi di tenant e sottoscrizioni con DisplayNames

Questa query usa il nuovo parametro **Include** con l'opzione _DisplayNames_ per aggiungere **subscriptionDisplayName** e **tenantDisplayName** ai risultati. Questo parametro è disponibile solo per l'interfaccia della riga di comando di Azure e Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Se la query non usa **project** per specificare le proprietà restituite, **subscriptionDisplayName** e **tenantDisplayName** vengono inclusi automaticamente nei risultati.
> Se la query usa **project**, tutti i campi _DisplayName_ devono essere inclusi in modo esplicito in **project**, altrimenti non verranno restituiti nei risultati, neanche se si usa il parametro **Include**.

## <a name="next-steps"></a>Passaggi successivi

- Vedere esempi di [query di base](starter.md)
- Altre informazioni sul [linguaggio di query](../concepts/query-language.md)
- Informazioni su come [esplorare le risorse](../concepts/explore-resources.md)