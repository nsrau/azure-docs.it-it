---
title: Diagnosticare e risolvere i problemi di query quando si usa Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi Azure Cosmos DB query SQL.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515809"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Risolvere i problemi relativi alle prestazioni delle query per Azure Cosmos DB
Questo articolo illustra come identificare, diagnosticare e risolvere i problemi Azure Cosmos DB query SQL. Per ottenere prestazioni ottimali per le query Azure Cosmos DB, attenersi alla procedura di risoluzione dei problemi riportata di seguito. 

## <a name="collocate-clients-in-same-azure-region"></a>Colloca i client nella stessa area di Azure 
È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere l'articolo [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) .

## <a name="check-consistency-level"></a>Verificare il livello di coerenza
Il [livello di coerenza](consistency-levels.md) può influisca sulle prestazioni e sugli addebiti Verificare che il livello di coerenza sia appropriato per lo scenario specificato. Per altri dettagli, vedere [scelta del livello di coerenza](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Registrare la query SQL eseguita 

È possibile registrare la query SQL eseguita in un account di archiviazione o nella tabella del log di diagnostica. Log di [query SQL tramite i log di diagnostica](logging.md#turn-on-logging-in-the-azure-portal) consente di registrare la query offuscata in un account di archiviazione di propria scelta. In questo modo è possibile esaminare i log e trovare la query che usa le UR più elevate. Successivamente, è possibile usare l'ID attività per trovare la corrispondenza con la query effettiva in QueryRuntimeStatistics. La query viene offuscata per finalità di sicurezza e i nomi dei parametri di query e i relativi valori nelle clausole WHERE sono diversi rispetto ai nomi e ai valori effettivi. È possibile usare la registrazione nell'account di archiviazione per mantenere la conservazione a lungo termine delle query eseguite.  

## <a name="log-query-metrics"></a>Metrica della query di log

Usare `QueryMetrics` per risolvere query lente o costose. 

  * `FeedOptions.PopulateQueryMetrics = true` Impostato`QueryMetrics` su nella risposta.
  * `QueryMetrics`la classe dispone di una `.ToString()` funzione in overload che può essere richiamata per ottenere la rappresentazione di stringa di. `QueryMetrics` 
  * Le metriche possono essere usate per derivare le informazioni seguenti, tra le altre: 
  
      * Indica se un componente specifico della pipeline di query ha richiesto un tempo di completamento anomalo (in ordine di centinaia di millisecondi o più). 

          * `TotalExecutionTime`Osservare.
          * Se la `TotalExecutionTime` della query è inferiore al tempo di esecuzione end-to-end, viene impiegato il tempo sul lato client o sulla rete. Verificare che il client e l'area di Azure siano collocati.
      
      * Indica se sono presenti falsi positivi nei documenti analizzati (se il numero di documenti di output è molto inferiore al numero di documenti recuperati).  

          * `Index Utilization`Osservare.
          * `Index Utilization`= (Numero di documenti restituiti/numero di documenti caricati)
          * Se il numero di documenti restituiti è molto inferiore al numero caricato, verranno analizzati i falsi positivi.
          * Limitare il numero di documenti recuperati con filtri più ristretti.  

      * Modalità di andata e ritorno dei singoli round trip `Partition Execution Timeline` (vedere dalla rappresentazione di `QueryMetrics`stringa di). 
      * Indica se la query ha utilizzato un costo elevato per le richieste. 

Per altri dettagli [, vedere l'articolo su come ottenere le metriche di esecuzione di query SQL](profile-sql-api-query.md) .
      
## <a name="tune-query-feed-options-parameters"></a>Ottimizzazione dei parametri di opzioni dei feed delle query 
Le prestazioni delle query possono essere ottimizzate tramite i parametri di [opzioni dei feed](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet). Provare a impostare le opzioni seguenti:

  * Impostare `MaxDegreeOfParallelism` su-1 prima, quindi confrontare le prestazioni tra valori diversi. 
  * Impostare `MaxBufferedItemCount` su-1 prima, quindi confrontare le prestazioni tra valori diversi. 
  * Impostare `MaxItemCount` su-1.

Quando si confrontano le prestazioni di diversi valori, provare a usare valori come 2, 4, 8, 16 e altri.
 
## <a name="read-all-results-from-continuations"></a>Leggere tutti i risultati dalle continuazioni
Se si ritiene che non siano visualizzati tutti i risultati, assicurarsi di esaurire completamente le continuazioni. In altre parole, continuare a leggere i risultati mentre il token di continuazione ha più documenti da sospendere.

È possibile esaurire completamente le continuazioni con uno dei modelli seguenti:

  * Continua l'elaborazione dei risultati mentre la continuazione non è vuota.
  * Continua l'elaborazione mentre la query contiene più risultati. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Scegliere le funzioni di sistema che utilizzano l'indice
Se l'espressione può essere convertita in un intervallo di valori stringa, può utilizzare l'indice; in caso contrario, non può. 

Di seguito è riportato l'elenco delle funzioni stringa che possono utilizzare l'indice: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ma solo se il primo num_expr è 0 
    
    Di seguito sono riportati alcuni esempi di query: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Evitare le funzioni di sistema nel filtro (o nella clausola WHERE) che non sono gestite dall'indice. Alcuni esempi di queste funzioni di sistema includono Contains, Upper e Lower.
  * Quando possibile, scrivere query per usare un filtro sulla chiave di partizione.
  * Per ottenere query performanti, evitare di chiamare la parte superiore/inferiore nel filtro. In alternativa, normalizzare la combinazione di maiuscole e minuscole dei valori all'inserimento Per ognuno dei valori, inserire il valore con la combinazione di maiuscole e minuscole desiderata oppure inserire il valore originale e il valore con la combinazione di maiuscole e minuscole desiderata. 

    Ad esempio:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Per questo caso, archiviare "JOE" in maiuscolo o archiviare sia "Joe" il valore originale che "JOE". 
    
    Se la combinazione di maiuscole e minuscole dei dati JSON viene normalizzata, la query diventa
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    La seconda query sarà più efficiente perché non richiede l'esecuzione di trasformazioni su ognuno dei valori per confrontare i valori con "JOE".

Per ulteriori informazioni sulla funzione di sistema, vedere l'articolo relativo alle [funzioni di sistema](sql-query-system-functions.md) .

## <a name="check-indexing-policy"></a>Controllare i criteri di indicizzazione
Per verificare che i [criteri di indicizzazione](index-policy.md) correnti siano ottimali:

  * Verificare che tutti i percorsi JSON usati nelle query siano inclusi nei criteri di indicizzazione per letture più veloci.
  * Escludi percorsi non utilizzati nelle query per scritture più performanti.

Per altri dettagli, vedere l'articolo [su come gestire i criteri di](how-to-manage-indexing-policy.md) indicizzazione.

## <a name="spatial-data-check-ordering-of-points"></a>Dati spaziali: Controllare l'ordine dei punti
I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.

## <a name="optimize-join-expressions"></a>Ottimizza espressioni di JOIN
`JOIN`le espressioni possono espandersi in prodotti incrociati di grandi dimensioni. Quando possibile, eseguire una query su uno spazio di ricerca più piccolo tramite un filtro più restrittivo.

Le sottoquery multivalore consentono di ottimizzare `JOIN` le espressioni eseguendo il `WHERE` push dei predicati dopo ogni espressione SELECT-many invece che dopo tutti i cross join nella clausola. Per un esempio dettagliato, vedere l'articolo relativo all' [ottimizzazione delle espressioni di join](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) .

## <a name="optimize-order-by-expressions"></a>Ottimizza espressioni ORDER BY 
`ORDER BY`le prestazioni delle query potrebbero risentirne se i campi sono di tipo sparse o non sono inclusi nei criteri di indice.

  * Per i campi di tipo sparse, ad esempio il tempo, ridurre il più possibile lo spazio di ricerca con i filtri. 
  * Per la proprietà `ORDER BY`singola, includere la proprietà nei criteri di indice. 
  * Per più espressioni `ORDER BY` di proprietà, definire un [indice composto](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) sui campi da ordinare.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Molti documenti di grandi dimensioni vengono caricati ed elaborati
Il tempo e le UR richieste da una query non dipendono solo dalle dimensioni della risposta, ma dipendono anche dal lavoro eseguito dalla pipeline di elaborazione delle query. I tempi e le UR aumentano proporzionalmente alla quantità di lavoro eseguita dall'intera pipeline di elaborazione delle query. Per i documenti di grandi dimensioni vengono eseguite altre operazioni, quindi è necessario più tempo e ur per caricare ed elaborare documenti di grandi dimensioni.

## <a name="low-provisioned-throughput"></a>Velocità effettiva con provisioning ridotto
Assicurarsi che la velocità effettiva con provisioning possa gestire il carico di lavoro. Aumentare il budget RU per le raccolte interessate.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Provare a eseguire l'aggiornamento alla versione più recente dell'SDK
Per determinare l'SDK più recente, vedere l'articolo relativo al [download e alle note sulla versione dell'SDK](sql-api-sdk-dotnet.md) .

## <a name="next-steps"></a>Passaggi successivi
Vedere i documenti riportati di seguito per informazioni su come misurare ur per query, ottenere statistiche di esecuzione per ottimizzare le query e altro ancora:

* [Ottenere le metriche di esecuzione delle query SQL usando .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)
