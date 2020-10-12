---
title: Usare set di dati di grandi dimensioni
description: Informazioni su come ottenere i record, formattarli, restituirli in pagine e ignorarli in set di dati di grandi dimensioni durante l'uso di Azure Resource Graph.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ee552908696aa652931bf3555391adcfec0fc6d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578496"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Utilizzo di set di dati della risorsa di Azure di grandi dimensioni

Azure Resource Graph è progettato per elaborare e ottenere informazioni sulle risorse nell'ambiente di Azure. Resource Graph rende più rapido il recupero di questi dati, anche in caso di query su migliaia di record. Resource Graph offre diverse opzioni per l'utilizzo di questi set di dati di grandi dimensioni.

Per informazioni sull'uso di query a frequenza elevata, vedere [Istruzioni per le richieste con limitazioni](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Dimensioni dei risultati dei set di dati

Per impostazione predefinita, Resource Graph limita a **100** record i risultati che possono essere restituiti da ogni query. Questa impostazione protegge sia l'utente che il servizio da query non intenzionali che restituirebbero set di dati di grandi dimensioni. Questo evento si verifica per lo più mentre l'utente fa diversi esperimenti con le query per trovare e filtrare le risorse nel modo più adeguato alle sue esigenze. Questa impostazione è diversa dall'uso degli operatori di linguaggio [top](/azure/kusto/query/topoperator) o [limit](/azure/kusto/query/limitoperator) di Esplora dati di Azure per limitare i risultati.

> [!NOTE]
> Quando si usa **First**, è consigliabile ordinare i risultati in base ad almeno una colonna con `asc` o `desc`. Senza ordinamento, i risultati restituiti sono casuali e non ripetibili.

È possibile eseguire l'override del limite predefinito tramite tutti i metodi di interazione con Resource Graph. Gli esempi seguenti mostrano come impostare il limite di dimensioni dei set di dati su _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Nell'[API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) il controllo è **$top** e fa parte di **QueryRequestOptions**.

Ha la priorità il controllo _più restrittivo_. Ad esempio, se la query usa l'operatore **top** o **limit** e la sua esecuzione restituirebbe più record rispetto a **First**, il numero massimo di record restituiti sarebbe uguale a **First**. Analogamente, se il valore di **top** o **limit** è inferiore a **First**, il set di record restituito sarebbe il valore più piccolo configurato da **top** o **limit**.

**First** ha attualmente un valore massimo consentito pari a _5000_ che raggiunge tramite la [paginazione dei risultati](#paging-results) _1000_ record alla volta.

> [!IMPORTANT]
> Quando **First** è configurato con un valore superiore a _1000_ record, la query deve **proiettare** il campo **ID** affinché la paginazione funzioni. Se questo non è presente nella query, la [paginazione](#paging-results) della risposta non viene eseguita e i risultati si limitano a _1000_ record.

## <a name="skipping-records"></a>Ignorare record

Un'altra opzione per l'elaborazione di set di dati di grandi dimensioni è il controllo **Skip**. Questo controllo consente alla query di saltare o ignorare il numero definito di record prima di restituire i risultati. **Skip** è utile per le query che ordinano i risultati in un modo significativo allo scopo di ottenere i record situati in una posizione centrale del set di risultati. Se i risultati desiderati sono alla fine del set di dati restituito, è più efficiente usare una configurazione di ordinamento diversa e recuperare i risultati dall'inizio del set di dati.

> [!NOTE]
> Quando si usa **Skip**, è consigliabile ordinare i risultati in base ad almeno una colonna con `asc` o `desc`. Senza ordinamento, i risultati restituiti sono casuali e non ripetibili. Se `limit` `take` nella query si utilizzano o, **Skip** viene ignorato.

Gli esempi seguenti mostrano come ignorare i primi _10_ record restituiti da una query facendo iniziare il set di risultati restituito dall'undicesimo record:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Nell'[API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) il controllo è **$skip** e fa parte di **QueryRequestOptions**.

## <a name="paging-results"></a>Risultati di paging

Se è necessario suddividere un set di risultati in set di record più piccoli ai fini dell'elaborazione o per evitare di superare il valore massimo consentito di _1000_ record restituiti, usare la paginazione. L' [API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** fornisce valori per indicare che un set di risultati è stato suddiviso: **resultTruncated** e **$skipToken**. **resultTruncated** è un valore booleano che informa il consumer se sono presenti record aggiuntivi non restituiti nella risposta. Questa condizione può essere identificata anche quando il valore della proprietà **count** è inferiore a quello della proprietà **totalRecords**. **totalRecords** definisce il numero di record che soddisfano la query.

 **resultTruncated** è **true** quando il paging è disabilitato o non è possibile perché nessuna `id` colonna o quando sono disponibili meno risorse rispetto a una query richiesta. Quando **resultTruncated** è **true**, la proprietà **$skipToken** non è impostata.

Gli esempi seguenti illustrano come **ignorare** i primi 3000 record e restituire i **primi** 1000 record dopo i record ignorati con l'interfaccia della riga di comando di Azure e Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Affinché la paginazione funzioni, la query deve **proiettare** il campo **ID**. Se questo non è presente nella query, la risposta non includerà **$skipToken**.

Per un esempio, vedere [Next page query](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) (Query della pagina successiva) nella documentazione dell'API REST.

## <a name="formatting-results"></a>Formattazione dei risultati

I risultati di una query di Resource Graph sono offerti in due formati: _Table_ e _ObjectArray_. Il formato viene configurato con il parametro **resultFormat** nell'ambito delle opzioni della richiesta. Il formato _Table_ è il valore predefinito per **resultFormat**.

Per impostazione predefinita, i risultati dell'interfaccia della riga di comando di Azure vengono restituiti in formato JSON. I risultati in Azure PowerShell sono, per impostazione predefinita, un elemento **PSCustomObject**, ma possono essere rapidamente convertiti in formato JSON usando il cmdlet `ConvertTo-Json`. Per gli altri SDK, è possibile configurare i risultati delle query in modo da restituire il formato _ObjectArray_.

### <a name="format---table"></a>Formato - Table

Il formato predefinito, _Table_, restituisce i risultati in un formato JSON ideato per evidenziare la struttura delle colonne e i valori di riga delle proprietà restituite dalla query. Questo formato è molto simile ai dati definiti in una tabella o un foglio di calcolo strutturato con le colonne identificate per prime e quindi ogni riga che rappresenta i dati allineati a queste colonne.

Di seguito è riportato un esempio del risultato di una query con la formattazione _Table_:

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

### <a name="format---objectarray"></a>Formato - ObjectArray

Anche il formato _ObjectArray_ restituisce i risultati in un formato JSON. Questa progettazione si allinea tuttavia alla relazione della coppia chiave/valore comune in JSON, in cui la colonna e i dati della riga vengono associati in gruppi di matrici.

Di seguito è riportato un esempio del risultato di una query con la formattazione _ObjectArray_:

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

Di seguito sono riportati alcuni esempi su come impostare **resultFormat** in modo che usi il formato _ObjectArray_:

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

- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).