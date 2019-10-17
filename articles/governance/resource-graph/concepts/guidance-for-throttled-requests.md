---
title: Istruzioni per le richieste con limitazioni
description: Informazioni su come creare query migliori per evitare la limitazione delle richieste al grafico delle risorse di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 1bbfd2a64de0b42da19d0a978874d564f1755c59
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387622"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Linee guida per le richieste limitate in Azure Resource Graph

Quando si crea un uso programmatico e frequente dei dati del grafo delle risorse di Azure, è necessario considerare il modo in cui la limitazione influisca sui risultati delle query. La modifica del modo in cui i dati vengono richiesti può aiutare l'utente e l'organizzazione a evitare la limitazione e mantenere il flusso dei dati tempestivi sulle risorse di Azure.

Questo articolo illustra quattro aree e modelli correlati alla creazione di query in Azure Resource Graph:

- Informazioni sulle intestazioni di limitazione
- Invio in batch di query
- Query di sfalsamento
- L'effetto della paginazione

## <a name="understand-throttling-headers"></a>Informazioni sulle intestazioni di limitazione

Il grafico risorse di Azure alloca il numero di quota per ogni utente in base a un intervallo di tempo. Ad esempio, un utente può inviare al massimo 15 query all'interno di ogni finestra di 5 secondi senza limitazioni. Il valore della quota è determinato da molti fattori ed è soggetto a modifiche.

In ogni risposta alla query, Azure Resource Graph aggiunge due intestazioni di limitazione:

- `x-ms-user-quota-remaining` (int): quota di risorse rimanente per l'utente. Questo valore è associato al conteggio delle query.
- `x-ms-user-quota-resets-after` (HH: mm: SS): durata dell'intervallo di tempo fino a quando non viene reimpostato il consumo della quota di un utente.

Per illustrare il funzionamento delle intestazioni, viene ora esaminata una risposta di query con l'intestazione e i valori di `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Nei prossimi 3 secondi, è possibile inviare al massimo 10 query senza limitazioni.
- In 3 secondi, i valori di `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` verranno reimpostati rispettivamente su `15` e `00:00:05`.

Per un esempio dell'uso delle intestazioni per _backoff_ sulle richieste di query, vedere l'esempio in [query in parallelo](#query-in-parallel).

## <a name="batching-queries"></a>Invio in batch di query

L'invio in batch di query in base alla sottoscrizione, al gruppo di risorse o a una singola risorsa è più efficiente delle query parallelizzazione. Il costo della quota di una query di dimensioni maggiori è spesso inferiore al costo della quota di molte query di piccole e di destinazione. È consigliabile che le dimensioni del batch siano minori di _300_.

- Esempio di approccio poco ottimizzato

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

- Esempio #1 di un approccio di batch ottimizzato

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
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Esempio #2 di un approccio di batch ottimizzato

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
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Query di sfalsamento

A causa del modo in cui viene applicata la limitazione, è consigliabile scaglionare le query. In altre termini, anziché inviare le query 60 contemporaneamente, è necessario sfalsare le query in quattro finestre di 5 secondi:

- Pianificazione di query non sfalsate

  | Conteggio query         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

- Pianificazione di query sfalsate

  | Conteggio query         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

Di seguito è riportato un esempio di come rispettare le intestazioni di limitazione durante l'esecuzione di query su Graph di risorse di Azure:

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

### <a name="query-in-parallel"></a>Esecuzione di query in parallelo

Anche se la suddivisione in batch è consigliata rispetto alla parallelizzazione, in alcuni casi non è possibile eseguire facilmente il batch delle query. In questi casi, è possibile eseguire una query sul grafico delle risorse di Azure inviando più query in modo parallelo. Di seguito è riportato un esempio di come _backoff_ in base alle intestazioni di limitazione in scenari di questo tipo:

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

## <a name="pagination"></a>Impaginazione

Poiché il grafico risorse di Azure restituisce al massimo 1000 voci in una singola risposta di query, potrebbe essere necessario [impaginare](./work-with-data.md#paging-results) le query per ottenere il set di dati completo che si sta cercando. Tuttavia, alcuni client di Azure Resource Graph gestiscono l'impaginazione in modo diverso rispetto ad altri.

- SDK per C#

  Quando si usa ResourceGraph SDK, è necessario gestire la paginazione passando il skip token restituito dalla risposta precedente alla query impaginata successiva. Questo progetto significa che è necessario raccogliere i risultati di tutte le chiamate impaginate e combinarli insieme alla fine. In questo caso, ogni query impaginata inviata accetta una quota di query:

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

- INTERFACCIA della riga di comando di Azure/Azure PowerShell

  Quando si usa l'interfaccia della riga di comando di Azure o Azure PowerShell, le query in Azure Resource Graph vengono impaginate automaticamente per recuperare al massimo 5000 voci. I risultati della query restituiscono un elenco combinato di voci da tutte le chiamate impaginate. In questo caso, a seconda del numero di voci nel risultato della query, una singola query impaginata può utilizzare più di una quota di query. Nell'esempio seguente, ad esempio, una singola esecuzione della query può utilizzare fino a cinque quote di query:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>È ancora stato limitato?

Se viene applicata la limitazione dopo aver eseguito i consigli indicati in precedenza, contattare il team al [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Specificare i dettagli seguenti:

- Il caso d'uso specifico e il driver aziendale necessitano di un limite di limitazione maggiore.
- Quante risorse sono accessibili? Il numero di restituzione di una singola query.
- Quali tipi di risorse sono interessati?
- Qual è il modello di query? X query per Y secondi e così via.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query Starter](../samples/starter.md).
- Vedere uso avanzato nelle [query avanzate](../samples/advanced.md).
- Scopri come [esplorare le risorse](explore-resources.md).