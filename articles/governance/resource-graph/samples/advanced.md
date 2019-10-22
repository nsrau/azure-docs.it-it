---
title: Esempi di query avanzate
description: Usare Azure Resource Graph per eseguire alcune query avanzate, ad esempio per ottenere la capacità di un set di scalabilità di macchine virtuali, elencare tutti i tag usati e cercare la corrispondenza di macchine virtuali con espressioni regolari.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387567"
---
# <a name="advanced-resource-graph-queries"></a>Query avanzate di Resource Graph

Il primo passaggio per capire le query con Azure Resource Graph è conoscere i fondamenti del [linguaggio di query](../concepts/query-language.md). Se non si ha familiarità con [Esplora dati di Azure](../../../data-explorer/data-explorer-overview.md), è consigliabile impararne le nozioni di base per capire come comporre richieste in grado di individuare le risorse desiderate.

Si esamineranno le query avanzate seguenti:

> [!div class="checklist"]
> - [Ottenere la capacità e le dimensioni di un set di scalabilità di macchine virtuali](#vmss-capacity)
> - [Rimuovere colonne dai risultati](#remove-column)
> - [Elencare tutti i nomi di tag](#list-all-tags)
> - [Macchine virtuali individuate da un'espressione regolare](#vm-regex)
> - [Visualizzare Cosmos DB con specifiche posizioni di scrittura](#mvexpand-cosmosdb)
> - [Insieme di credenziali delle chiavi con il nome della sottoscrizione](#join)
> - [Visualizzare i database SQL e i relativi pool elastici](#join-sql)
> - [Visualizzare le macchine virtuali con relative interfacce di rete e IP pubblici](#join-vmpip)
> - [Trovare account di archiviazione con un tag specifico del gruppo di risorse](#join-findstoragetag)
> - [Combinare i risultati di due query in un singolo risultato](#unionresults)
> - [Includere i nomi di tenant e sottoscrizioni con DisplayNames](#displaynames)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="language-support"></a>Supporto per le lingue

Azure Resource Graph è supportato dall'interfaccia della riga di comando di Azure tramite un'estensione e da Azure PowerShell tramite un modulo. Prima di eseguire le query seguenti, verificare che l'ambiente sia pronto. Per i passaggi necessari per installare e convalidare l'ambiente shell preferito, vedere le informazioni relative all'[interfaccia della riga di comando di Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e ad [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="vmss-capacity"></a>Ottenere la capacità e le dimensioni di un set di scalabilità di macchine virtuali

Questa query cerca risorse di set di scalabilità di macchine virtuali e ottiene vari dettagli fra cui le dimensioni della macchina virtuale e la capacità del set di scalabilità. Questa query usa la funzione `toint()` per eseguire il cast della capacità in un numero, in modo che possa essere ordinato. Infine, le colonne vengono rinominate in proprietà denominate personalizzate.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Rimuovere colonne dai risultati

La query seguente usa `summarize` per contare le risorse in base a sottoscrizione, `join` per aggiungere i dettagli della sottoscrizione della tabella _ResourceContainers_, quindi `project-away` per rimuovere alcune colonne.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Elencare tutti i nomi di tag

Questa query inizia con il tag e compila un oggetto JSON che elenca tutti i nomi di tag univoci e i tipi corrispondenti.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
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
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Visualizzare Cosmos DB con specifiche posizioni di scrittura

La query seguente si limita alle risorse di Cosmos DB, usa `mv-expand` per espandere il contenitore di proprietà per **properties.writeLocations**, quindi i campi specifici del progetto, e per limitare ulteriormente i risultati ai valori di **properties.writeLocations.locationName** che corrispondono a 'Stati Uniti orientali' o 'Stati Uniti occidentali'.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Insieme di credenziali delle chiavi con il nome della sottoscrizione

La query seguente illustra un uso complesso di `join`. La query limita la tabella aggiunta alle risorse della sottoscrizione e usa `project` per includere solo il campo _subscriptionId_ originale e il campo _name_ rinominato in _SubName_. La ridenominazione del campo evita che `join` lo aggiunga come _name1_, perché il campo esiste già in _resources_. La tabella originale viene filtrata con `where` e l'elemento `project` seguente include colonne di entrambe le tabelle. Il risultato della query è un singolo insieme di credenziali delle chiavi che visualizza il tipo, il nome dell'insieme di credenziali delle chiavi e il nome della sottoscrizione in cui è incluso.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>Visualizzare i database SQL e i relativi pool elastici

La query seguente usa **leftouter** `join` per raggruppare le risorse del database SQL e i relativi pool elastici, se disponibili.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Visualizzare le macchine virtuali con relative interfacce di rete e IP pubblici

Questa query usa due comandi **leftouter** `join` per raggruppare le macchine virtuali, le relative interfacce di rete e qualsiasi indirizzo IP pubblico correlato a tali interfacce di rete.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Trovare account di archiviazione con un tag specifico del gruppo di risorse

La query seguente usa **innerunique** `join` per connettere gli account di archiviazione ai gruppi di risorse con un nome e un valore di tag specificati.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Combinare i risultati di due query in un singolo risultato

La query seguente usa `union` per ottenere i risultati della tabella _ResourceContainers_ e aggiungerli ai risultati della tabella _Resources_.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
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