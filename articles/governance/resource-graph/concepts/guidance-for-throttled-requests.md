---
title: Istruzioni per le richieste con limitazioni
description: Informazioni per il raggruppamento, la distribuzione, l’impaginazione e l’esecuzione di query in parallelo per evitare che le richieste vengano limitate da Azure Resource Graph.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: dbcd438f1eda4edd30deef41542beeae6d746dc2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682052"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Istruzioni per le richieste con limitazioni in Azure Resource Graph

Quando si pianifica un uso programmatico e frequente dei dati di Azure Resource Graph, è necessario considerare il modo in cui la limitazione influisce sui risultati delle query. La modifica del modo in cui i dati vengono richiesti può aiutare l'utente e l'organizzazione a evitare limitazioni e mantenere il flusso di dati tempestivi sulle risorse di Azure.

Questo articolo illustra quattro aree e modelli correlati alla creazione di query in Azure Resource Graph:

- Informazioni sulle intestazioni di limitazione
- Raggruppamento di query
- Distribuzione di query
- Effetti della paginazione

## <a name="understand-throttling-headers"></a>Informazioni sulle intestazioni di limitazione

Azure Resource Graph alloca un numero di quota per ogni utente in base a un intervallo di tempo. Ad esempio, un utente può inviare al massimo 15 query all'interno di ogni intervallo di 5 secondi senza limitazioni. Il valore della quota è determinato da molti fattori ed è soggetto a modifiche.

In ogni risposta alle query, Azure Resource Graph aggiunge due intestazioni di limitazione:

- `x-ms-user-quota-remaining` (int): quota di risorse rimanenti per l'utente. Questo valore è associato al conteggio delle query.
- `x-ms-user-quota-resets-after` (hh:mm:ss): durata temporale fino alla reimpostazione del consumo della quota dell'utente.

Per illustrare il funzionamento delle intestazioni, viene ora esaminata una risposta di query con l'intestazione e i valori di `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Nei prossimi 3 secondi, è possibile inviare al massimo 10 query senza limitazioni.
- Dopo 3 secondi, i valori di `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` verranno reimpostati rispettivamente su `15` e `00:00:05`.

Per un esempio dell'uso delle intestazioni per il _backoff_ delle richieste di query, vedere l'esempio in [Query in parallelo](#query-in-parallel).

## <a name="grouping-queries"></a>Raggruppamento di query

Il raggruppamento delle query in base alla sottoscrizione, al gruppo di risorse o a una singola risorsa è più efficiente della parallelizzazione delle query. Il costo della quota di una query di dimensioni maggiori è spesso inferiore al costo della quota di molte query piccole e mirate. È consigliabile che le dimensioni del gruppo siano inferiori a _300_.

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

- Esempio 1 di un approccio di raggruppamento ottimizzato

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

- Esempio 2 di un approccio di raggruppamento ottimizzato per ottenere più risorse in un'unica query

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

A causa del modo in cui viene applicata la limitazione, è consigliabile sfalsare le query. In altri termini, anziché inviare 60 query contemporaneamente, è necessario sfalsare le query in quattro intervalli di 5 secondi:

- Pianificazione di query non sfalsate

  | Conteggio query         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (secondi) | 0-5 | 5-10 | 10-15 | 15-20 |

- Pianificazione di query sfalsate

  | Conteggio query         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervallo di tempo (secondi) | 0-5 | 5-10 | 10-15 | 15-20 |

Di seguito è riportato un esempio di come rispettare le intestazioni di limitazione durante l'esecuzione di query in Azure Resource Graph:

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

Anche se è consigliabile eseguire il raggruppamento piuttosto che la parallelizzazione, in alcuni casi non è possibile raggruppare facilmente le query. In questi casi, è possibile eseguire query in Azure Resource Graph inviando più query in modo parallelo. Di seguito è riportato un esempio di come eseguire il _backoff_ in base alle intestazioni di limitazione in scenari di questo tipo:

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

Poiché Azure Resource Graph restituisce al massimo 1000 voci in una singola risposta di query, potrebbe essere necessario [impaginare](./work-with-data.md#paging-results) le query per ottenere il set di dati completo richiesto. Tuttavia, alcuni client di Azure Resource Graph gestiscono l'impaginazione in modo diverso rispetto ad altri.

- SDK per C#

  Quando si usa l’SDK ResourceGraph, è necessario gestire la paginazione passando lo skip token restituito dalla risposta della query precedente alla query impaginata successiva. Questo significa che è necessario raccogliere i risultati di tutte le chiamate impaginate e combinarli insieme alla fine. In questo caso, ogni query impaginata inviata prende una quota di query:

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

- Interfaccia della riga di comando di Azure/Azure PowerShell

  Quando si usa l'interfaccia della riga di comando di Azure o Azure PowerShell, le query in Azure Resource Graph vengono impaginate automaticamente per recuperare al massimo 5000 voci. I risultati della query restituiscono un elenco combinato di voci da tutte le chiamate impaginate. In questo caso, a seconda del numero di voci nel risultato della query, una singola query impaginata può utilizzare più di una quota di query. Nell'esempio seguente, una singola esecuzione della query può utilizzare fino a cinque quote di query:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Applicazione di limitazioni

Se viene comunque applicata la limitazione dopo aver seguito gli elementi consigliati precedenti, contattare il team all'indirizzo [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Specificare questi dettagli:

- Il caso d'uso specifico e le esigenze aziendali per cui è necessario un limite superiore per la limitazione.
- Il numero di risorse a cui si ha accesso. Il numero di risorse restituite da una singola query.
- I tipi di risorse rilevanti.
- Il modello di query usato. X query per Y secondi, e così via.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).