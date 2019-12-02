---
title: Esempi di query di base
description: Usare Azure Resource Graph per eseguire alcune query di base, tra cui conteggio delle risorse, ordinamento delle risorse o ordinamento con un tag specifico.
ms.date: 11/21/2019
ms.topic: sample
ms.openlocfilehash: b966d8c239cb6ff706c967174bcea23bf25de374
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279436"
---
# <a name="starter-resource-graph-query-samples"></a>Esempi di query di Resource Graph di base

Il primo passaggio per capire le query con Azure Resource Graph è conoscere i fondamenti del [linguaggio di query](../concepts/query-language.md). Se non si ha familiarità con il [linguaggio di query Kusto (KQL)](/azure/kusto/query/index), è consigliabile vedere l'[esercitazione per KQL](/azure/kusto/query/tutorial) per capire come comporre richieste in grado di individuare le risorse desiderate.

Si esamineranno le query di base seguenti:

> [!div class="checklist"]
> - [Contare le risorse di Azure](#count-resources)
> - [Contare le risorse dell'insieme di credenziali delle chiavi](#count-keyvaults)
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
> - [Visualizzare i gruppi di sicurezza di rete non associati](#unassociated-nsgs)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="language-support"></a>Supporto per le lingue

Azure Resource Graph è supportato dall'interfaccia della riga di comando di Azure tramite un'estensione e da Azure PowerShell tramite un modulo. Prima di eseguire le query seguenti, verificare che l'ambiente sia pronto. Per i passaggi necessari per installare e convalidare l'ambiente shell preferito, vedere le informazioni relative all'[interfaccia della riga di comando di Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e ad [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="a-namecount-resources-count-azure-resources"></a><a name="count-resources" />Contare le risorse di Azure

Questa query restituisce il numero di risorse di Azure esistenti nelle sottoscrizioni a cui l'utente ha accesso. È anche una query valida da usare per verificare che nella shell preferita siano installati i componenti di Azure Resource Graph appropriati e funzionino correttamente.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a><a name="count-keyvaults" />Contare le risorse dell'insieme di credenziali delle chiavi

Questa query usa `count` invece di `summarize` per contare il numero di record restituiti. Nel conteggio sono inclusi solo gli insiemi di credenziali delle chiavi.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />Elencare le risorse ordinate per nome

Questa query restituisce qualsiasi tipo di risorsa, ma solo le proprietà **name**, **type** e **location**. Usa `order by` per ordinare le proprietà in base alla proprietà **name** in ordine crescente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Mostrare tutte le macchine virtuali ordinate per nome in ordine decrescente

Per elencare solo le macchine virtuali (che sono di tipo `Microsoft.Compute/virtualMachines`), è possibile cercare le corrispondenze della proprietà **type** nei risultati. Analogamente alla query precedente, `desc` cambia `order by` in decrescente. Nella ricerca di corrispondenze per tipo, `=~` induce Resource Graph a non distinguere fra maiuscole e minuscole.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo

Questa query userà `top` per recuperare solo cinque record corrispondenti che vengono ordinati in base al nome. Il tipo della risorsa di Azure è `Microsoft.Compute/virtualMachines`. `project` indica ad Azure Resource Graph quali proprietà includere.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />Contare le macchine virtuali per tipo di sistema operativo

Continuando dalla query precedente, le risorse sono ancora limitate al tipo `Microsoft.Compute/virtualMachines`, ma non è più presente il limite sul numero di record restituiti.
Invece, sono stati usati `summarize` e `count()` per definire come raggruppare e aggregare i valori in base a una proprietà, che in questo esempio è `properties.storageProfile.osDisk.osType`. Per un esempio dell'aspetto di questa stringa nell'oggetto completo, vedere le informazioni sull'[esplorazione delle risorse e l'individuazione delle macchine virtuali](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

Un modo diverso per scrivere la stessa query consiste nell'applicare `extend` a una proprietà assegnando un nome temporaneo da usare all'interno della query, in questo caso **os**. **os** viene quindi usato da `summarize` e `count()` come nell'esempio precedente.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

> [!NOTE]
> Tenere presente che, sebbene `=~` consenta la ricerca di corrispondenze senza distinzione fra maiuscole e minuscole, per usare le proprietà, ad esempio **properties.storageProfile.osDisk.osType**, nella query è necessario che le maiuscole/minuscole siano corrette. Se le maiuscole/minuscole sono errate, viene restituito un valore Null o errato e il raggruppamento o il riepilogo non saranno corretti.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />Mostrare le risorse che contengono archivi

Invece di definire in modo esplicito il tipo da individuare, questa query di esempio troverà tutte le risorse di Azure che contengono (`contains`) la parola **storage**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />Elencare tutti gli indirizzi IP pubblici

Analogamente alla query precedente, trovare gli elementi in cui il tipo contenga la parola **publicIPAddresses**.
Questa query si basa su tale modello per includere solo i risultati con **properties.ipAddress**
`isnotempty`, in modo da restituire solo **properties.ipAddress** e a `limit` i risultati migliori
100. Potrebbe essere necessario eseguire l'escape delle virgolette a seconda della shell in uso.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Contare le risorse che hanno indirizzi IP configurati per sottoscrizione

Usando la query dell'esempio precedente e aggiungendo `summarize` e `count()`, è possibile ottenere un elenco per sottoscrizione delle risorse con indirizzi IP configurati.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Elencare le risorse con un valore di tag specifico

È possibile limitare i risultati mediante proprietà diverse dal tipo di risorsa di Azure, ad esempio un tag. In questo esempio si applica alle risorse di Azure un filtro basato sul nome di tag **Environment** con il valore **Internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

Per fornire anche i tag della risorsa e i relativi valori, aggiungere la proprietà **tags** per alla parola chiave `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Elencare tutti gli account di archiviazione con un valore di tag specifico

Combinare la funzionalità di filtro dell'esempio precedente e filtrare il tipo di risorsa di Azure per la proprietà **type**. Questa query limita anche la ricerca di tipi specifici di risorse di Azure con un nome di tag e un valore specifici.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

> [!NOTE]
> Questo esempio usa `==` per la corrispondenza anziché l'operatore condizionale `=~`. `==` rappresenta una corrispondenza con distinzione fra maiuscole e minuscole.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Mostrare gli alias per una risorsa di macchina virtuale

[Gli alias dei criteri di Azure](../../policy/concepts/definition-structure.md#aliases) vengono usati da Criteri di Azure per gestire la conformità delle risorse. Azure Resource Graph può restituire gli _alias_ di un tipo di risorsa. Questi valori sono utili per confrontare il valore corrente degli alias durante la creazione di una definizione di criteri personalizzata. La matrice degli _alias_ non viene fornita per impostazione predefinita nei risultati di una query. Usare `project aliases` per aggiungerla in modo esplicito ai risultati.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Mostrare valori distinti per un alias specifico

Vedere il valore degli alias in una singola risorsa è utile, ma non mostra il vero valore dell'uso di Azure Resource Graph per le query tra sottoscrizioni. Questo esempio esamina tutti i valori di un alias specifico e restituisce i valori distinti.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Visualizzare i gruppi di sicurezza di rete non associati

Questa query restituisce i gruppi di sicurezza di rete non associati a un'interfaccia di rete o a una subnet.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

![Icona di Resource Graph Explorer](../media/resource-graph-small.png) Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)
- Portale di Azure Cina: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Icona Apri collegamento in una nuova finestra](../../media/new-window.png)

---

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [linguaggio di query](../concepts/query-language.md).
- Altre informazioni su come [esplorare risorse](../concepts/explore-resources.md).
- Vedere esempi di [query avanzate](advanced.md).