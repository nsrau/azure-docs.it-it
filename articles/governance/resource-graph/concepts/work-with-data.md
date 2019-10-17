---
title: Usare set di dati di grandi dimensioni
description: Informazioni su come ottenere e controllare set di dati di grandi dimensioni durante l'utilizzo di Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: c78f2e37fa29fa1cdcb9acc6a4600688750b6d74
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387584"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Utilizzo di set di dati della risorsa di Azure di grandi dimensioni

Azure Resource Graph è progettato per elaborare e ottenere informazioni sulle risorse nell'ambiente di Azure. Resource Graph rende più rapido il recupero di questi dati, anche in caso di query su migliaia di record. Resource Graph offre diverse opzioni per l'utilizzo di questi set di dati di grandi dimensioni.

Per istruzioni sull'uso delle query a frequenza elevata, vedere [linee guida per le richieste limitate](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Dimensioni dei risultati dei set di dati

Per impostazione predefinita, Resource Graph limita a **100** record i risultati che possono essere restituiti da ogni query. Questa impostazione protegge sia l'utente che il servizio da query non intenzionali che restituirebbero set di dati di grandi dimensioni. Questo evento si verifica per lo più mentre l'utente fa diversi esperimenti con le query per trovare e filtrare le risorse nel modo più adeguato alle sue esigenze. Questa impostazione è diversa dall'uso degli operatori di linguaggio [top](/azure/kusto/query/topoperator) o [limit](/azure/kusto/query/limitoperator) di Esplora dati di Azure per limitare i risultati.

> [!NOTE]
> Quando si usa **First**, è consigliabile ordinare i risultati in base a almeno una colonna con `asc` o `desc`. Senza ordinamento, i risultati restituiti sono casuali e non ripetibili.

È possibile eseguire l'override del limite predefinito tramite tutti i metodi di interazione con Resource Graph. Gli esempi seguenti mostrano come impostare il limite di dimensioni dei set di dati su _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Nell'[API REST](/rest/api/azureresourcegraph/resources/resources) il controllo è **$top** e fa parte di **QueryRequestOptions**.

Ha la priorità il controllo _più restrittivo_. Ad esempio, se la query usa l'operatore **top** o **limit** e la sua esecuzione restituirebbe più record rispetto a **First**, il numero massimo di record restituiti sarebbe uguale a **First**. Analogamente, se il valore di **top** o **limit** è inferiore a **First**, il set di record restituito sarebbe il valore più piccolo configurato da **top** o **limit**.

Attualmente il valore massimo consentito per **First** è _5000_.

## <a name="skipping-records"></a>Ignorare record

Un'altra opzione per l'elaborazione di set di dati di grandi dimensioni è il controllo **Skip**. Questo controllo consente alla query di saltare o ignorare il numero definito di record prima di restituire i risultati. **Skip** è utile per le query che ordinano i risultati in un modo significativo allo scopo di ottenere i record situati in una posizione centrale del set di risultati. Se i risultati desiderati sono alla fine del set di dati restituito, è più efficiente usare una configurazione di ordinamento diversa e recuperare i risultati dall'inizio del set di dati.

> [!NOTE]
> Quando si usa **Skip**, è consigliabile ordinare i risultati in base a almeno una colonna con `asc` o `desc`. Senza ordinamento, i risultati restituiti sono casuali e non ripetibili.

Gli esempi seguenti mostrano come ignorare i primi _10_ record restituiti da una query facendo iniziare il set di risultati restituito dall'undicesimo record:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Nell'[API REST](/rest/api/azureresourcegraph/resources/resources) il controllo è **$skip** e fa parte di **QueryRequestOptions**.

## <a name="paging-results"></a>Risultati di paging

Quando è necessario suddividere un set di risultati in set di record più piccoli per l'elaborazione o perché un set di risultati supera il valore massimo consentito di _1000_ record restituiti, utilizzare il paging. L'[API REST](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** fornisce i valori **resultTruncated** e **$skipToken** per indicare se un set di risultati è stato suddiviso.
**resultTruncated** è un valore booleano che informa il consumer se sono presenti record aggiuntivi non restituiti nella risposta. Questa condizione può essere identificata anche quando il valore della proprietà **count** è inferiore a quello della proprietà **totalRecords**. **totalRecords** definisce il numero di record che soddisfano la query.

Quando **resultTruncated** è **true**, la proprietà **$skipToken** è impostata nella risposta. Questo valore viene usato con gli stessi valori di query e sottoscrizione per ottenere il set di record successivo che soddisfa la query.

Gli esempi seguenti illustrano come **ignorare** i primi record 3000 e restituire i **primi** 1000 record dopo che questi record sono stati IGNORAti con l'interfaccia della riga di comando di Azure e Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Affinché la paginazione funzioni, la query deve **proiettare** il campo **ID**. Se non è presente nella query, la risposta non includerà il **$skipToken**.

Per un esempio, vedere [Next page query](/rest/api/azureresourcegraph/resources/resources#next-page-query) (Query della pagina successiva) nella documentazione dell'API REST.

## <a name="formatting-results"></a>Formattazione dei risultati

I risultati di una query di Graph di risorse sono disponibili in due formati, _Table_ e _ObjectArray_. Il formato viene configurato con il parametro **ResultFormat** come parte delle opzioni di richiesta. Il formato della _tabella_ è il valore predefinito per **ResultFormat**.

I risultati dell'interfaccia della riga di comando di Azure sono disponibili in JSON per impostazione predefinita. I risultati in Azure PowerShell sono **PSCustomObject** per impostazione predefinita, ma possono essere convertiti rapidamente in JSON usando il cmdlet `ConvertTo-Json`. Per gli altri SDK, i risultati della query possono essere configurati in modo da restituire il formato _ObjectArray_ .

### <a name="format---table"></a>Format-Table

Il formato predefinito, _Table_, restituisce i risultati in un formato JSON progettato per evidenziare la progettazione della colonna e i valori di riga delle proprietà restituite dalla query. Questo formato è molto simile ai dati definiti in una tabella strutturata o in un foglio di calcolo con le colonne identificate prima e quindi ogni riga che rappresenta i dati allineati a tali colonne.

Di seguito è riportato un esempio di risultato di una query con la formattazione della _tabella_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format-ObjectArray

Il formato _ObjectArray_ restituisce anche i risultati in formato JSON. Questa progettazione è tuttavia allineata alla relazione tra coppie chiave/valore comune in JSON, in cui la colonna e i dati di riga corrispondono ai gruppi di matrici.

Di seguito è riportato un esempio di risultato di una query con la formattazione _ObjectArray_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Di seguito sono riportati alcuni esempi di impostazione di **ResultFormat** per l'uso del formato _ObjectArray_ :

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query Starter](../samples/starter.md).
- Vedere uso avanzato nelle [query avanzate](../samples/advanced.md).
- Scopri come [esplorare le risorse](explore-resources.md).