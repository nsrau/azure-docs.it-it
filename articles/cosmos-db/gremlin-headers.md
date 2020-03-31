---
title: Intestazioni di risposta di Azure Cosmos DB GremlinAzure Cosmos DB Gremlin response headers
description: Documentazione di riferimento per i metadati di risposta del server che consentono ulteriori procedure di risoluzione dei problemi
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755089"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Intestazioni di risposta del server Azure Cosmos DB GremlinAzure Cosmos DB Gremlin server response headers
Questo articolo illustra le intestazioni che il server Gremlin di Cosmos DB restituisce al chiamante al momento dell'esecuzione della richiesta. Queste intestazioni sono utili per la risoluzione dei problemi relativi alle prestazioni delle richieste, per la compilazione di applicazioni che si integrano in modo nativo con il servizio Cosmos DB e per semplificare il supporto clienti.

Tenere presente che l'assunzione di dipendenza da queste intestazioni si limita la portabilità dell'applicazione ad altre implementazioni Gremlin.Keep in mind that taking dependency on these headers you are limiting portability of your application to other Gremlin implementations. In cambio, stai actueando una più stretta integrazione con Cosmos DB Gremlin. Queste intestazioni non sono uno standard TinkerPop.

## <a name="headers"></a>Headers

| Intestazione | Type | Valore di esempio | Quando incluso | Spiegazione |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success and Failure | Quantità di velocità effettiva di raccolta o database utilizzata in [unità di richiesta (RU/s o RU)](request-units.md) per un messaggio di risposta parziale. Questa intestazione è presente in ogni continuazione per le richieste con più blocchi. Riflette l'addebito di un particolare blocco di risposta. Solo per le richieste costituite da un singolo blocco di risposta questa intestazione corrisponde al costo totale di attraversamento. Tuttavia, per la maggior parte degli attraversamenti complessi questo valore rappresenta un costo parziale. |
| **x-ms-totale-richiesta-carica** | double | 423.987 | Success and Failure | Quantità di velocità effettiva di raccolta o database utilizzata in [unità di richiesta (RU/s o RU)](request-units.md) per l'intera richiesta. Questa intestazione è presente in ogni continuazione per le richieste con più blocchi. Indica un addebito cumulativo dall'inizio della richiesta. Il valore di questa intestazione nell'ultimo blocco indica l'addebito completo della richiesta. |
| **x-ms-server-time-ms** | double | 13,75 | Success and Failure | Questa intestazione è inclusa per la risoluzione dei problemi di latenza. Indica la quantità di tempo, in millisecondi, impiegato dal server Cosmos DB Gremlin per l'esecuzione e la produzione di un messaggio di risposta parziale. L'utilizzo del valore di questa intestazione e il confronto con le applicazioni di latenza delle richieste complessive possono calcolare l'overhead di latenza di rete. |
| **x-ms-totale-server-time-ms** | double | 130.512 | Success and Failure | Tempo totale, in millisecondi, impiegato dal server Cosmos DB Gremlin per eseguire l'intero attraversamento. Questa intestazione è inclusa in ogni risposta parziale. Rappresenta il tempo di esecuzione cumulativo dall'inizio della richiesta. L'ultima risposta indica il tempo di esecuzione totale. Questa intestazione è utile per distinguere tra client e server come origine di latenza. È possibile confrontare il tempo di esecuzione dell'attraversamento sul client con il valore di questa intestazione. |
| **x-ms-status-code** | long | 200 | Success and Failure | Intestazione indica il motivo interno per il completamento o la chiusura della richiesta. L'applicazione consiglia di esaminare il valore di questa intestazione e intraprendere azioni correttive. |
| **x-ms-substatus-code** | long | 1003 | Solo errore | Cosmos DB è un database multimodello basato su un livello di archiviazione unificato. Questa intestazione contiene informazioni aggiuntive sul motivo dell'errore quando si verifica un errore all'interno di livelli inferiori dello stack di disponibilità elevata. Si consiglia di memorizzare questa intestazione e utilizzarla quando si contatta l'assistenza clienti Cosmos DB. Il valore di questa intestazione è utile per Cosmos DB Engineer per una rapida risoluzione dei problemi. |
| **x-ms-retry-after-ms** | string (TimeSpan) | "00:00:03.9500000" | Solo errore | Questa intestazione è una rappresentazione di stringa di un tipo [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) .NET. Questo valore verrà incluso solo nelle richieste non riuscita esaurimento della velocità effettiva di provisioning. L'applicazione deve inviare nuovamente l'attraversamento dopo un periodo di tempo indicato. |
| **x-ms-activity-id** | string (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | Intestazione contiene un identificatore univoco sul lato server di una richiesta. A ogni richiesta viene assegnato un identificatore univoco dal server a scopo di rilevamento. Le applicazioni devono registrare gli identificatori di attività restituiti dal server per le richieste che i clienti potrebbero voler contattare il supporto clienti su. Il personale di supporto di Cosmos DB può trovare richieste specifiche da questi identificatori nella telemetria del servizio Cosmos DB. |

## <a name="status-codes"></a>Codici di stato

Di seguito sono elencati i codici di stato più comuni restituiti dal server.

| Stato | Spiegazione |
| --- | --- |
| **401** | Viene `"Unauthorized: Invalid credentials provided"` restituito un messaggio di errore quando la password di autenticazione non corrisponde alla chiave dell'account Cosmos DB. Passare all'account Cosmos DB Gremlin nel portale di Azure e verificare che la chiave sia corretta.|
| **404** | Operazioni simultanee che tentano di eliminare e aggiornare contemporaneamente lo stesso bordo o vertice. Il messaggio di errore `"Owner resource does not exist"` indica che il database o la raccolta specificati non sono corretti nei parametri di connessione in formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"`indica che l'attraversamento ha richiesto più di **30 secondi** ed è stato annullato dal server. Ottimizza i tuoi attraversamenti per essere eseguito rapidamente filtrando vertici o bordi su ogni hop di attraversamento per restringere l'ambito di ricerca.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Generalmente questo si verifica quando nel grafo esiste già un vertice o un arco con un identificatore.| 
| **412** | Il codice di stato `"PreconditionFailedException": One of the specified pre-condition is not met`è completato dal messaggio di errore . Questo errore indica una violazione del controllo della concorrenza ottimistica tra la lettura di un bordo o un vertice e la sua scrittura nell'archivio dopo la modifica. Le situazioni più comuni quando si verifica `g.V('identifier').property('name','value')`questo errore sono la modifica di proprietà, ad esempio . Il motore Gremlin leggeva il vertice, lo modificava e lo riscriveva. Se è presente un altro attraversamento in esecuzione in parallelo durante il tentativo di scrivere lo stesso vertice o un bordo, uno di essi riceverà questo errore. L'applicazione deve inviare nuovamente l'attraversamento al server.| 
| **429** | La richiesta è stata limitata e deve essere ritentata dopo il valore in** x-ms-retry-after-ms**| 
| **500** | Il messaggio di errore contenente `"NotFoundException: Entity with the specified id does not exist in the system."` indica che sono stati ricreati un database e/o una raccolta con lo stesso nome. L'errore scomparirà entro 5 minuti perché la modifica si propaga e invalida le cache in componenti Cosmos DB diversi. Per evitare il problema, usare ogni volta i nomi univoci per database e raccolte.| 
| **1000** | Questo codice di stato viene restituito quando il server ha analizzato correttamente un messaggio ma non è stato in grado di eseguire. In genere indica un problema con la query.| 
| **1001** | Questo codice viene restituito quando il server completa l'esecuzione dell'attraversamento, ma non riesce a serializzare la risposta al client. Questo errore può verificarsi quando l'attraversamento genera un risultato complesso, troppo grande o non conforme alla specifica del protocollo TinkerPop. L'applicazione deve semplificare l'attraversamento quando si verifica questo errore. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`viene restituito quando l'attraversamento supera il limite di memoria consentito. Il limite di memoria è **di 2 GB** per attraversamento.| 
| **1004** | Questo codice di stato indica una richiesta di grafico in formato non valido. La richiesta può essere in formato non corretto quando non riesce la deserializzazione, il tipo non di valore viene deserializzato come tipo di valore o come operazione gremlin non supportata richiesta. L'applicazione non deve ritentare la richiesta perché non avrà esito positivo. | 
| **1007** | In genere questo codice di `"Could not process request. Underlying connection has been closed."`stato viene restituito con il messaggio di errore . Questa situazione può verificarsi se il driver client tenta di utilizzare una connessione che viene chiusa dal server. L'applicazione deve ritentare l'attraversamento su una connessione diversa.
| **1008** | Cosmos DB Gremlin server può terminare le connessioni per ribilanciare il traffico nel cluster. I driver client devono gestire questa situazione e utilizzare solo connessioni attive per inviare richieste al server. Occasionalmente i driver client potrebbero non rilevare che la connessione è stata chiusa. Quando l'applicazione rileva `"Connection is too busy. Please retry after sometime or open more connections."` un errore, deve ritentare l'attraversamento su una connessione diversa.

## <a name="samples"></a>Esempi

Un'applicazione client di esempio basata su Gremlin.Net che legge un attributo di stato:A sample client application based on Gremlin.Net that reads one status attribute:

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

Un esempio che illustra come leggere l'attributo status dal client Java Gremlin:An example that demonstrates how to read status attribute from Gremlin java client:

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
* [HTTP status codes for Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Intestazioni comuni di risposta REST di Azure Cosmos DBCommon Azure Cosmos DB REST response headers](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisiti del provider di driver TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
