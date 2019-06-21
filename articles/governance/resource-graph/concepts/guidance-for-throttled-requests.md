---
title: Linee guida per le richieste limitate
description: Informazioni su come creare query migliorate per evitare le richieste di Graph di Azure Resource da limitata.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276899"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Linee guida per le richieste limitate nel grafico di risorse di Azure

Durante la creazione di utilizzo a livello di codice e frequente dei dati dei grafi di risorse di Azure, considerazione deve essere effettuata per influisce sul modo in cui limitazione i risultati delle query. La modifica dei dati di modalità richiesta utili e organizzazione evitare limitazioni e mantenere il flusso di dati in modo tempestivo sulle risorse di Azure.

Questo articolo descrive quattro aree e i modelli correlati per la creazione di query nel grafico di risorse di Azure:

- Comprendere le intestazioni di limitazione delle richieste
- L'invio in batch query
- Scaglionamento query
- L'impatto di impaginazione

## <a name="understand-throttling-headers"></a>Comprendere le intestazioni di limitazione delle richieste

Graph di Azure Resource alloca il numero di quota per ogni utente in base a un intervallo di tempo. Ad esempio, un utente può inviare al massimo 15 query all'interno di ogni finestra di 5 secondi senza limitazioni. Il valore di quota è determinato da diversi fattori e è soggette a modifiche.

In ogni risposta alla query, Graph di Azure Resource aggiunge due intestazioni di limitazione delle richieste:

- `x-ms-user-quota-remaining` (int): quota di risorse rimanenti per l'utente. Questo valore è associato al conteggio delle query.
- `x-ms-user-quota-resets-after` (hh:mm:ss): La durata di tempo fino a quando non viene reimpostato il consumo di quota dell'utente.

Per illustrare come funzionano le intestazioni, esaminiamo una risposta alla query con l'intestazione e i valori della `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Entro 3 secondi, al massimo 10 query possono essere inviate senza limitazioni.
- In 3 secondi e i valori della `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` reimposteranno `15` e `00:00:05` rispettivamente.

Per vedere un esempio dell'uso di intestazioni _backoff_ le richieste di query, vedere l'esempio nella [Query in parallelo](#query-in-parallel).

## <a name="batching-queries"></a>L'invio in batch query

Query per la sottoscrizione, gruppo di risorse o singola risorsa di invio in batch è più efficiente rispetto alla parallelizzazione delle query. Il costo di quota di una query di dimensioni maggiori è spesso inferiore rispetto al costo di quota di numerose query di piccole dimensioni e di destinazione. Le dimensioni del batch consigliata sarà minore _300_.

- Esempio di approccio non ottimizzato

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Esempio #1 di un approccio di invio in batch ottimizzato

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Ad esempio #2 un approccio di invio in batch ottimizzato

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Scaglionamento query

A causa della modalità viene applicata la limitazione delle richieste, è consigliabile usare le query a essere sfalsate. Vale a dire, anziché inviare 60 query al contempo, scaglionare le query nelle quattro finestre di 5 secondi:

- Pianificazione scaglionati non query

  | Conteggio di query         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

- Scaglionati pianificazione di query

  | Conteggio di query         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

Di seguito è riportato un esempio di il rispettando le intestazioni di limitazione delle richieste quando si eseguono query di Graph di Azure Resource:

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

Anche se l'invio in batch è preferibile la parallelizzazione, vi sono casi in cui le query non possono essere facilmente eseguite in batch. In questi casi, è possibile eseguire una query di Graph di risorse di Azure mediante l'invio di più query in parallelo. Di seguito è riportato un esempio relativo alla _backoff_ base di intestazioni in questi scenari di limitazione:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

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

Poiché Azure risorsa Graph restituisce al massimo 1000 voci in risposta a una singola query, potrebbe essere necessario [impaginare](./work-with-data.md#paging-results) le query per ottenere il set di dati completo, si sta cercando. Tuttavia, alcuni client Graph di Azure Resource gestire in modo diverso rispetto ad altri la paginazione.

- SDK per C#

  Quando si usa ResourceGraph SDK, è necessario gestire la paginazione passando il token skip restituito dalla risposta alla query precedente per la query successiva che impaginata. Questa progettazione significa che è necessario raccogliere i risultati di tutte le chiamate impaginate e combinarli alla fine. In questo caso, ogni query impaginati che si inviano accetta una quota di query:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
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

- Comando di Azure / Azure PowerShell

  Quando si usa Azure PowerShell o CLI di Azure, le query di Graph di Azure Resource vengono automaticamente impaginate per il recupero di al massimo 5000 voci. I risultati della query di restituire un elenco combinato di voci da tutte le chiamate impaginate. In questo caso, a seconda del numero di voci nel risultato della query, una singola query impaginate può usare più di una quota di query. Nell'esempio seguente, ad esempio, una singola esecuzione della query potrebbe usare quota query fino a cinque:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Ancora applicata la limitazione?

Se sono state applicate limitazioni dopo che esercitano consigli sopra indicati, contattare il team all'indirizzo [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Fornire questi dettagli:

- Le esigenze di driver specifico caso d'uso e di business per un aumento del limite della limitazione.
- Il numero di risorse è possibile utilizzare? Il numero di controllo vengono restituiti da una singola query?
- Quali sono i tipi di risorse ti interessa?
- Che cos'è il modello di query? X query ogni Y secondi e così via.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nel [query Starter](../samples/starter.md).
- Vedere avanzata utilizza [query avanzate](../samples/advanced.md).
- Informazioni su come [esplorare risorse](explore-resources.md).