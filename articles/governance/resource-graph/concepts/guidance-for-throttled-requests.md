---
title: Istruzioni per le richieste con limitazioni
description: Informazioni su come raggruppare, scaglionare, impaginare ed eseguire query in parallelo per evitare che le richieste vengano limitate da Azure Resource Graph.Learn to group, sgger, paginate, and query in parallel to avoid requests being throttled by Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259851"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Indicazioni per le richieste limitate in Azure Resource GraphGuidance for throttled requests in Azure Resource Graph

Quando si creano dati a livello di codice e frequenti di Azure Resource Graph, è necessario tenere conto dell'impatto delle limitazioni sui risultati delle query. La modifica della modalità di richiesta dei dati consente a te e alla tua organizzazione di evitare di essere limitati e mantenere il flusso di dati tempestivi sulle risorse di Azure.

Questo articolo illustra quattro aree e modelli correlati alla creazione di query in Azure Resource Graph:This article covers four areas and patterns related to the creation of queries in Azure Resource Graph:

- Informazioni sulle intestazioni di limitazione
- Raggruppamento di query
- Distribuzione di query
- L'impatto dell'impaginazione

## <a name="understand-throttling-headers"></a>Informazioni sulle intestazioni di limitazione

Azure Resource Graph alloca il numero di quota per ogni utente in base a un intervallo di tempo. Ad esempio, un utente può inviare al massimo 15 query all'interno di ogni finestra di 5 secondi senza essere limitato. Il valore della quota è determinato da molti fattori ed è soggetto a modifiche.

In ogni risposta alla query, Azure Resource Graph aggiunge due intestazioni di limitazione:In every query response, Azure Resource Graph adds two throttling headers:

- `x-ms-user-quota-remaining`(int): quota di risorse rimanente per l'utente. Questo valore è associato al conteggio delle query.
- `x-ms-user-quota-resets-after`(hh:mm:ss): durata fino a quando il consumo di quote di un utente viene reimpostato.

Per illustrare il funzionamento delle intestazioni, esaminiamo una risposta alla `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03`query con l'intestazione e i valori di e .

- Entro i prossimi 3 secondi, al massimo 10 query possono essere inviate senza essere limitate.
- In 3 secondi, `x-ms-user-quota-remaining` i `x-ms-user-quota-resets-after` valori di `15` `00:00:05` e verranno reimpostati rispettivamente su e rispettivamente.

Per un esempio di utilizzo delle intestazioni per eseguire il _backoff_ delle richieste di query, vedere l'esempio in [Query in parallelo](#query-in-parallel).

## <a name="grouping-queries"></a>Raggruppamento di query

Il raggruppamento delle query in base alla sottoscrizione, al gruppo di risorse o a una singola risorsa è più efficiente della parallelizzazione delle query. Il costo di quota di una query più grande è spesso inferiore al costo di quota di molte query piccole e mirate. Si consiglia di utilizzare una dimensione del gruppo inferiore a _300_.

- Esempio di approccio mal ottimizzato

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Esempio #1 di un approccio di raggruppamento ottimizzatoExample for of an optimized grouping approach

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Esempio #2 di un approccio di raggruppamento ottimizzato per ottenere più risorse in una queryExample #2 of an optimized grouping approach for getting multiple resources in one query

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Distribuzione di query

A causa del modo in cui viene applicata la limitazione delle richieste, è consigliabile scaglionare le query. Ovvero, invece di inviare 60 query contemporaneamente, sfalsare le query in quattro finestre di 5 secondi:

- Pianificazione delle query non sfalsate

  | Query Count         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

- Pianificazione delle query sfalsate

  | Query Count         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

Di seguito è riportato un esempio di rispetto delle intestazioni di limitazione quando si esegue una query su Azure Resource Graph:Below is an example of respecting throttling headers when querying Azure Resource Graph:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Query in parallelo

Anche se il raggruppamento è consigliato durante la parallelizzazione, in alcuni casi le query non possono essere raggruppate facilmente. In questi casi, è possibile eseguire una query di Azure Resource Graph inviando più query in modo parallelo. Di seguito è riportato un esempio di come _eseguire il backoff_ in base alle intestazioni di limitazione in tali scenari:Below is an example of how to backoff based on throttling headers in such scenarios:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginazione

Poiché Azure Resource Graph restituisce al massimo 1000 voci in una singola risposta di query, potrebbe essere necessario [impaginare](./work-with-data.md#paging-results) le query per ottenere il set di dati completo che si sta cercando. Tuttavia, alcuni client di Azure Resource Graph gestiscono l'impaginazione in modo diverso rispetto ad altri.

- SDK per C#

  Quando si usa ResourceGraph SDK, è necessario gestire l'impaginazione passando il token di salto restituito dalla risposta alla query precedente alla query impaginata successiva. Questo design significa che è necessario raccogliere i risultati da tutte le chiamate impaginate e combinarli insieme alla fine. In questo caso, ogni query impaginata inviata richiede una quota di query:In this case, each paginated query you send takes one query quota:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  Quando si usa l'interfaccia della riga di comando di Azure o Azure PowerShell, le query a Azure Resource Graph vengono automaticamente impaginate per recuperare al massimo 5000 voci. I risultati della query restituiscono un elenco combinato di voci da tutte le chiamate impaginate. In questo caso, a seconda del numero di voci nel risultato della query, una singola query impaginata può consumare più di una quota di query. Ad esempio, nell'esempio seguente, una singola esecuzione della query può consumare fino a cinque quote di query:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Ancora essere limitato?

Se si sta ottenendo limitato dopo aver esercitato i consigli di [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)cui sopra, contattare il team a .

Fornire questi dettagli:Provide these details:

- Il caso d'uso specifico e il driver di business devono soddisfare un limite di limitazione maggiore.
- A quante risorse hai accesso? Quanti vengono restituiti da una singola query?
- Quali tipi di risorse sono interessati?
- Qual è il tuo modello di query? Query X per secondi Y ecc.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la lingua in uso nelle [query Starter](../samples/starter.md).
- Vedere Utilizzo avanzato nelle [query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).