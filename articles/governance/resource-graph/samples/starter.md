---
title: Esempi di query di base
description: Usare Azure Resource Graph per eseguire alcune query di base, tra cui conteggio delle risorse, ordinamento delle risorse o ordinamento con un tag specifico.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 688c591cbe94c69c73779843011cb24c3d2fd4cf
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241087"
---
# <a name="starter-resource-graph-queries"></a>Query di base di Resource Graph

Il primo passaggio per capire le query con Azure Resource Graph è conoscere i fondamenti del [linguaggio di query](../concepts/query-language.md). Se non si ha familiarità con [Esplora dati di Azure](../../../data-explorer/data-explorer-overview.md), è consigliabile impararne le nozioni di base per capire come comporre richieste in grado di individuare le risorse desiderate.

Si esamineranno le query di base seguenti:

> [!div class="checklist"]
> - [Contare le risorse di Azure](#count-resources)
> - [Elencare le risorse ordinate per nome](#list-resources)
> - [Mostrare tutte le macchine virtuali ordinate per nome in ordine decrescente](#show-vms)
> - [Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](#show-sorted)
> - [Contare le macchine virtuali per tipo di sistema operativo](#count-os)
> - [Mostrare le risorse che contengono archivi](#show-storage)
> - [Elencare tutti gli indirizzi IP pubblici](#list-publicip)
> - [Contare le risorse che hanno indirizzi IP configurati per sottoscrizione](#count-resources-by-ip)
> - [Elencare le risorse con un valore di tag specifico](#list-tag)
> - [Elencare tutti gli account di archiviazione con un valore di tag specifico](#list-specific-tag)
> - [Mostrare gli alias per una risorsa di macchina virtuale](#show-aliases)
> - [Mostrare valori distinti per un alias specifico](#distinct-alias-values)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="language-support"></a>Supporto per le lingue

Azure Resource Graph è supportato dall'interfaccia della riga di comando di Azure tramite un'estensione e da Azure PowerShell tramite un modulo. Prima di eseguire le query seguenti, verificare che l'ambiente sia pronto. Per i passaggi necessari per installare e convalidare l'ambiente shell preferito, vedere le informazioni relative all'[interfaccia della riga di comando di Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e ad [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Contare le risorse di Azure

Questa query restituisce il numero di risorse di Azure esistenti nelle sottoscrizioni a cui l'utente ha accesso. È anche una query valida da usare per verificare che nella shell preferita siano installati i componenti di Azure Resource Graph appropriati e funzionino correttamente.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Elencare le risorse ordinate per nome

Questa query restituisce qualsiasi tipo di risorsa, ma solo le proprietà **name**, **type** e **location**. Usa `order by` per ordinare le proprietà in base alla proprietà **name** in ordine crescente (`asc`).

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Mostrare tutte le macchine virtuali ordinate per nome in ordine decrescente

Per elencare solo le macchine virtuali (che sono di tipo `Microsoft.Compute/virtualMachines`), è possibile cercare le corrispondenze della proprietà **type** nei risultati. Analogamente alla query precedente, `desc` cambia `order by` in decrescente. Nella ricerca di corrispondenze per tipo, `=~` induce Resource Graph a non distinguere fra maiuscole e minuscole.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo

Questa query userà `top` per recuperare solo cinque record corrispondenti che vengono ordinati in base al nome. Il tipo della risorsa di Azure è `Microsoft.Compute/virtualMachines`. `project` indica ad Azure Resource Graph quali proprietà includere.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Contare le macchine virtuali per tipo di sistema operativo

Continuando dalla query precedente, le risorse sono ancora limitate al tipo `Microsoft.Compute/virtualMachines`, ma non è più presente il limite sul numero di record restituiti.
Invece, sono stati usati `summarize` e `count()` per definire come raggruppare e aggregare i valori in base a una proprietà, che in questo esempio è `properties.storageProfile.osDisk.osType`. Per un esempio dell'aspetto di questa stringa nell'oggetto completo, vedere le informazioni sull'[esplorazione delle risorse e l'individuazione delle macchine virtuali](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Un modo diverso per scrivere la stessa query consiste nell'applicare `extend` a una proprietà assegnando un nome temporaneo da usare all'interno della query, in questo caso **os**. **os** viene quindi usato da `summarize` e `count()` come nell'esempio precedente.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Tenere presente che, sebbene `=~` consenta la ricerca di corrispondenze senza distinzione fra maiuscole e minuscole, per usare le proprietà, ad esempio **properties.storageProfile.osDisk.osType**, nella query è necessario che le maiuscole/minuscole siano corrette. Se le maiuscole/minuscole sono errate, la query può comunque restituire un valore, ma il raggruppamento o il riepilogo non saranno corretti.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Mostrare le risorse che contengono archivi

Invece di definire in modo esplicito il tipo da individuare, questa query di esempio troverà tutte le risorse di Azure che contengono (`contains`) la parola **storage**.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Elencare tutti gli indirizzi IP pubblici

Analogamente alla query precedente, trovare gli elementi in cui il tipo contenga la parola **publicIPAddresses**.
Questa query si basa su tale modello per includere solo i risultati con **properties.ipAddress**
`isnotempty`, in modo da restituire solo **properties.ipAddress** e a `limit` i risultati migliori
100. Potrebbe essere necessario eseguire l'escape delle virgolette a seconda della shell in uso.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Contare le risorse che hanno indirizzi IP configurati per sottoscrizione

Usando la query dell'esempio precedente e aggiungendo `summarize` e `count()`, è possibile ottenere un elenco per sottoscrizione delle risorse con indirizzi IP configurati.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Elencare le risorse con un valore di tag specifico

È possibile limitare i risultati mediante proprietà diverse dal tipo di risorsa di Azure, ad esempio un tag. In questo esempio si applica alle risorse di Azure un filtro basato sul nome di tag **Environment** con il valore **Internal**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Per fornire anche i tag della risorsa e i relativi valori, aggiungere la proprietà **tags** per alla parola chiave `project`.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Elencare tutti gli account di archiviazione con un valore di tag specifico

Combinare la funzionalità di filtro dell'esempio precedente e filtrare il tipo di risorsa di Azure per la proprietà **type**. Questa query limita anche la ricerca di tipi specifici di risorse di Azure con un nome di tag e un valore specifici.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Questo esempio usa `==` per la corrispondenza anziché l'operatore condizionale `=~`. `==` rappresenta una corrispondenza con distinzione fra maiuscole e minuscole.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Mostrare gli alias per una risorsa di macchina virtuale

[Gli alias dei criteri di Azure](../../policy/concepts/definition-structure.md#aliases) vengono usati da Criteri di Azure per gestire la conformità delle risorse. Azure Resource Graph può restituire gli _alias_ di un tipo di risorsa. Questi valori sono utili per confrontare il valore corrente degli alias durante la creazione di una definizione di criteri personalizzata. La matrice degli _alias_ non viene fornita per impostazione predefinita nei risultati di una query. Usare `project aliases` per aggiungerla in modo esplicito ai risultati.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Mostrare valori distinti per un alias specifico

Vedere il valore degli alias in una singola risorsa è utile, ma non mostra il vero valore dell'uso di Azure Resource Graph per le query tra sottoscrizioni. Questo esempio esamina tutti i valori di un alias specifico e restituisce i valori distinti.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [linguaggio di query](../concepts/query-language.md)
- Informazioni su come [esplorare le risorse](../concepts/explore-resources.md)
- Vedere esempi di [query avanzate](advanced.md)
