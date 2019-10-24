---
title: Intestazioni di risposta Azure Cosmos DB Gremlin
description: Documentazione di riferimento per i metadati di risposta del server che Abilita la risoluzione dei problemi aggiuntiva
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755089"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Intestazioni di risposta del server Azure Cosmos DB Gremlin
Questo articolo illustra le intestazioni che Cosmos DB server Gremlin restituisce al chiamante al momento dell'esecuzione della richiesta. Queste intestazioni sono utili per la risoluzione dei problemi relativi alle prestazioni delle richieste, per la compilazione di applicazioni che si integrano in modo nativo con Cosmos DB servizio e semplificano il supporto clienti.

Tenere presente che la dipendenza da queste intestazioni consente di limitare la portabilità dell'applicazione ad altre implementazioni Gremlin. In cambio, si ottiene un'integrazione più stretta con Cosmos DB Gremlin. Queste intestazioni non sono standard TinkerPop.

## <a name="headers"></a>headers

| Intestazione | Type | Valore di esempio | Quando incluso | Spiegazione |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | Double | 11,3243 | Esito positivo e negativo | Quantità di velocità effettiva raccolta o database utilizzata in [unità richiesta (UR/s o UR)](request-units.md) per un messaggio di risposta parziale. Questa intestazione è presente in ogni continuazione per le richieste con più blocchi. Riflette il costo di un determinato blocco di risposta. Solo per le richieste che sono costituite da un singolo blocco di risposta questa intestazione corrisponde al costo totale di attraversamento. Tuttavia, per la maggior parte degli attraversamenti complessi, questo valore rappresenta un costo parziale. |
| **x-ms-total-request-charge** | Double | 423,987 | Esito positivo e negativo | Quantità di velocità effettiva raccolta o database utilizzata nelle [unità richiesta (UR/s o UR)](request-units.md) per l'intera richiesta. Questa intestazione è presente in ogni continuazione per le richieste con più blocchi. Indica un addebito cumulativo dall'inizio della richiesta. Il valore di questa intestazione nell'ultimo blocco indica un addebito completo della richiesta. |
| **x-MS-Server-time-MS** | Double | 13,75 | Esito positivo e negativo | Questa intestazione è inclusa per la risoluzione dei problemi di latenza. Indica la quantità di tempo, in millisecondi, impiegato da Cosmos DB server Gremlin per eseguire e produrre un messaggio di risposta parziale. Il valore di questa intestazione e il confronto con le applicazioni di latenza richieste complessive possono calcolare l'overhead della latenza di rete. |
| **x-ms-Total-Server-time-MS** | Double | 130,512 | Esito positivo e negativo | Tempo totale, in millisecondi, impiegato per l'esecuzione di un intero attraversamento da Cosmos DB server Gremlin. Questa intestazione è inclusa in ogni risposta parziale. Rappresenta il tempo di esecuzione cumulativo dall'avvio della richiesta. L'ultima risposta indica il tempo di esecuzione totale. Questa intestazione è utile per distinguere tra client e server come origine della latenza. È possibile confrontare il tempo di esecuzione di attraversamento del client con il valore di questa intestazione. |
| **x-ms-status-code** | long | 200 | Esito positivo e negativo | L'intestazione indica la causa interna del completamento o della terminazione della richiesta. Si consiglia all'applicazione di esaminare il valore di questa intestazione e intraprendere un'azione correttiva. |
| **x-ms-SubStatus-code** | long | 1003 | Solo errore | Cosmos DB è un database multimodello basato sul livello di archiviazione unificato. Questa intestazione contiene informazioni aggiuntive sulla causa dell'errore quando si verifica un errore all'interno di livelli inferiori dello stack di disponibilità elevata. È consigliabile archiviare questa intestazione e usarla quando si contatta Cosmos DB supporto tecnico. Il valore di questa intestazione è utile per Cosmos DB Engineer per la risoluzione rapida dei problemi. |
| **x-ms-retry-after-ms** | stringa (TimeSpan) | "00:00:03.9500000" | Solo errore | Questa intestazione è una rappresentazione di stringa di un tipo .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) . Questo valore verrà incluso solo nelle richieste non riuscite a causa dell'esaurimento della velocità effettiva con provisioning. L'applicazione deve inviare nuovamente l'attraversamento dopo il periodo di tempo indicato. |
| **x-ms-activity-id** | stringa (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Esito positivo e negativo | L'intestazione contiene un identificatore univoco del lato server di una richiesta. A ogni richiesta viene assegnato un identificatore univoco da parte del server a scopo di verifica. Le applicazioni devono registrare gli identificatori di attività restituiti dal server per le richieste a cui i clienti possono rivolgersi per contattare il supporto tecnico. Cosmos DB il personale di supporto può trovare richieste specifiche da tali identificatori nella telemetria del servizio Cosmos DB. |

## <a name="status-codes"></a>Codici di stato

Di seguito sono elencati i codici di stato più comuni restituiti dal server.

| Status | Spiegazione |
| --- | --- |
| **401** | Il messaggio di errore `"Unauthorized: Invalid credentials provided"` viene restituito quando la password di autenticazione non corrisponde Cosmos DB chiave dell'account. Passare all'account Cosmos DB Gremlin nel portale di Azure e verificare che la chiave sia corretta.|
| **404** | Operazioni simultanee che tentano di eliminare e aggiornare contemporaneamente lo stesso bordo o vertice. Il messaggio di errore `"Owner resource does not exist"` indica che il database o la raccolta specificati non sono corretti nei parametri di connessione in formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"` indica che l'attraversamento ha impiegato più di **30 secondi** ed è stato annullato dal server. Ottimizzare gli attraversamenti per l'esecuzione rapida filtrando i vertici o i bordi su ogni hop di attraversamento per limitare l'ambito di ricerca.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` questa situazione si verifica in genere quando nel grafico esiste già un vertice o un bordo con un identificatore.| 
| **412** | Il codice di stato è integrato con il messaggio di errore `"PreconditionFailedException": One of the specified pre-condition is not met`. Questo errore indica una violazione del controllo della concorrenza ottimistica tra la lettura di un bordo o un vertice e la riscrittura nell'archivio dopo la modifica. La maggior parte delle situazioni comuni in cui si verifica questo errore è la modifica delle proprietà, ad esempio `g.V('identifier').property('name','value')`. Il motore Gremlin legge il vertice, lo modifica e lo scrive di nuovo. Se è presente un altro attraversamento in esecuzione in parallelo che tenta di scrivere lo stesso vertice o un bordo, uno di essi riceverà questo errore. L'applicazione deve inviare nuovamente l'attraversamento al server.| 
| **429** | La richiesta è stata limitata e deve essere ritentata dopo il valore in **x-ms-Retry-After-MS**| 
| **500** | Messaggio di errore contenente `"NotFoundException: Entity with the specified id does not exist in the system."` indica che un database e/o una raccolta sono stati ricreati con lo stesso nome. L'errore scomparirà entro 5 minuti perché la modifica si propaga e invalida le cache in componenti Cosmos DB diversi. Per evitare il problema, usare ogni volta i nomi univoci per database e raccolte.| 
| **1000** | Questo codice di stato viene restituito quando il server ha analizzato correttamente un messaggio ma non è stato possibile eseguirlo. Indica in genere un problema con la query.| 
| **1001** | Questo codice viene restituito quando il server completa l'esecuzione attraversata, ma non riesce a serializzare la risposta al client. Questo errore può verificarsi quando l'attraversamento genera un risultato complesso, troppo grande o non è conforme alla specifica del protocollo TinkerPop. L'applicazione dovrebbe semplificare l'attraversamento quando si verifica questo errore. | 
| **1003** | Quando l'attraversamento supera il limite di memoria consentito, viene restituito `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`. Il limite di memoria è di **2 GB** per attraversamento.| 
| **1004** | Questo codice di stato indica una richiesta di grafo non valido. Il formato della richiesta può non essere corretto quando la deserializzazione non riesce, il tipo non valore viene deserializzato come tipo di valore o è richiesta un'operazione Gremlin non supportata. L'applicazione non deve ripetere la richiesta perché non avrà esito positivo. | 
| **1007** | Questo codice di stato viene in genere restituito con il messaggio di errore `"Could not process request. Underlying connection has been closed."`. Questa situazione può verificarsi se il driver client tenta di utilizzare una connessione chiusa dal server. L'applicazione deve ritentare l'attraversamento in una connessione diversa.
| **1008** | Cosmos DB server Gremlin può terminare le connessioni per ribilanciare il traffico nel cluster. I driver client devono gestire questa situazione e utilizzare solo le connessioni in tempo reale per inviare richieste al server. Occasionalmente i driver client potrebbero non rilevare che la connessione è stata chiusa. Quando l'applicazione rileva un errore, `"Connection is too busy. Please retry after sometime or open more connections."` deve ritentare l'attraversamento in una connessione diversa.

## <a name="samples"></a>Esempi

Applicazione client di esempio basata su Gremlin.Net che legge un attributo di stato:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Esempio che illustra come leggere l'attributo status dal client Java Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Passaggi successivi
* [Codici di stato HTTP per Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Intestazioni di risposta comuni Azure Cosmos DB REST](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisiti del provider del driver Graph TinkerPop]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
