---
title: Diagnosticare e risolvere i problemi delle query quando si usa Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi di query SQL di Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835827"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Risolvere i problemi di prestazioni delle query per Azure Cosmos DB
Questo articolo illustra come identificare, diagnosticare e risolvere i problemi di query SQL di Azure Cosmos DB. Per ottenere prestazioni ottimali per le query di Azure Cosmos DB, seguire questa procedura di risoluzione dei problemi. 

## <a name="collocate-clients-in-same-azure-region"></a>Collocare i client nella stessa area di Azure 
È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) articolo.

## <a name="check-consistency-level"></a>Controllare il livello di coerenza
[Livello di coerenza](consistency-levels.md) può influire sulle prestazioni e costi. Assicurarsi che il livello di coerenza è appropriato per questo scenario specifico. Per altre informazioni, vedere [scelta a livello di coerenza](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Metriche di query di log
Usare `QueryMetrics` per risolvere i problemi di query lenta o costosa. 

  * Impostare `FeedOptions.PopulateQueryMetrics = true` avere `QueryMetrics` nella risposta.
  * `QueryMetrics` classe ha un overload `.ToString()` funzione che può essere richiamata per ottenere la rappresentazione di stringa di `QueryMetrics`. 
  * Le metriche possono essere utilizzate per derivare le informazioni dettagliate seguenti, tra gli altri: 
  
      * Indica se qualsiasi componente specifico della pipeline di query ha richiesto in modo anomalo tempo (in ordine di centinaia di millisecondi o più). 

          * Esaminare `TotalExecutionTime`.
          * Se il `TotalExecutionTime` della query è inferiore al tempo di esecuzione to end e quindi l'uso del tempo sul lato client o dalla rete. Verificare che il client e l'area di Azure sono collocati.
      
      * Se si sono falsi positivi nei documenti analizzati (se il numero di documenti di Output è molto inferiore rispetto a numero di documenti recuperati).  

          * Esaminare `Index Utilization`.
          * `Index Utilization` = (Restituito un numero di documenti / numero di caricare i documenti)
          * Se il numero di documenti restituiti dalla query è molto inferiore a quello caricato, vengono quindi vengono analizzati i falsi positivi.
          * Limitare il numero di documenti recuperati con i filtri più ristretti.  

      * Round trip dei singoli fared (vedere la `Partition Execution Timeline` dalla rappresentazione di stringa di `QueryMetrics`). 
      * Indica se la query ha utilizzato addebito richiesta elevata. 

Per altre informazioni, vedere [come ottenere metriche di esecuzione delle query SQL](profile-sql-api-query.md) articolo.
      
## <a name="tune-query-feed-options-parameters"></a>Ottimizzare i parametri delle opzioni del Feed di Query 
È possibile ottimizzare le prestazioni delle query tramite la richiesta [Feed opzioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parametri. Provare a impostare le opzioni seguenti:

  * Impostare `MaxDegreeOfParallelism` a-1 prima e quindi confrontare le prestazioni tra valori diversi. 
  * Impostare `MaxBufferedItemCount` a-1 prima e quindi confrontare le prestazioni tra valori diversi. 
  * Impostare `MaxItemCount` su -1.

Quando si confrontano le prestazioni di diversi valori, provare a valori, ad esempio 2, 4, 8, 16 e ad altri utenti.
 
## <a name="read-all-results-from-continuations"></a>Leggere tutti i risultati dalle continuazioni
Se si ritiene che non si ottengono tutti i risultati, verificare che svuotare completamente la continuazione. In altre parole, mantenere la lettura dei risultati mentre il token di continuazione dispone di più documenti da restituire.

Completamente lo svuotamento può essere ottenuto con uno dei modelli seguenti:

  * Continuare l'elaborazione dei risultati mentre continuazione non è vuota.
  * Continuare l'elaborazione durante la query include più risultati. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Scegliere le funzioni di sistema che si avvalgono dell'indice
Se l'espressione può essere convertito in un intervallo di valori stringa, è possibile usare l'indice; in caso contrario, questo non è possibile. 

Ecco l'elenco delle funzioni di stringa che possono usare l'indice: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING (num_expr str_expr, num_expr) = str_expr, ma solo se prima num_expr è 0 
    
    Ecco alcuni esempi di query: 
    
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

  * Evitare funzioni di sistema nel filtro (o la clausola WHERE) che non vengono servite dall'indice. Alcuni esempi di tali funzioni di sistema includono Contains, superiore, inferiore.
  * Quando possibile, scrivere query per usare un filtro sulla chiave di partizione.
  * Per ottenere ad alte prestazioni query di evitare le chiamate a maiuscola/minuscola nel filtro. Al contrario, normalizzazione di maiuscole e minuscole dei valori al momento dell'inserimento. Per ognuno dei valori di inserire il valore con maiuscole e minuscole desiderata, o inserire il valore originale e il valore con le maiuscole e minuscole desiderato. 

    Ad esempio:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    In questo caso, store "JOE" maiuscola o archiviare il valore originale "Joe" sia "JOE". 
    
    Se le maiuscole e minuscole dei dati JSON sono normalizzato secondo la query diventa:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    La seconda query sarà più efficiente perché non richiede l'esecuzione di trasformazioni su ognuno dei valori per confrontare i valori con "JOE".

Per altre funzioni di sistema informazioni dettagliate, vedere [funzioni di sistema](sql-query-system-functions.md) articolo.

## <a name="check-indexing-policy"></a>Controllare i criteri di indicizzazione
Per verificare che l'oggetto corrente [criteri di indicizzazione](index-policy.md) ottimale:

  * Verificare che tutti i percorsi JSON usati nelle query vengono inclusi nei criteri di indicizzazione per operazioni di lettura più veloci.
  * Escludere i percorsi non usati nelle query per le altre scritture ad alte prestazioni.

Per altre informazioni, vedere [modalità per gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md) articolo.

## <a name="spatial-data-check-ordering-of-points"></a>Dati spaziali: Controllare l'ordinamento dei punti
I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.

## <a name="optimize-join-expressions"></a>Ottimizzare le espressioni di JOIN
`JOIN` le espressioni è possono espandere in grandi tra prodotti. Quando possibile, query su una ricerca più piccola spazio tramite un filtro più stretto.

Sottoquery con più valori è possibile ottimizzare `JOIN` espressioni premendo predicati dopo ogni espressione select-molti anziché dopo tutti i cross join nel `WHERE` clausola. Per un esempio dettagliato, vedere [ottimizzare l'unione di espressioni](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) articolo.

## <a name="optimize-order-by-expressions"></a>Ottimizzare le espressioni ORDER BY 
`ORDER BY` le prestazioni delle query potrebbero subire se i campi sono di tipo sparse o non è incluso nei criteri di indice.

  * Per i campi di tipo sparse, ad esempio tempo, ridurre lo spazio di ricerca quanto più possibile con i filtri. 
  * Per la singola proprietà `ORDER BY`, includere proprietà nei criteri di indice. 
  * Per la proprietà più `ORDER BY` espressioni, definire un [indice composto](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) nei campi da ordinare.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Molti documenti di grandi dimensioni viene caricato ed elaborato
Il tempo e UR necessarie per una query non sono solo dipende dalle dimensioni della risposta, ma sono anche dipende da ciò che avviene tramite la pipeline di elaborazione di query. Tempo e UR proporzionale alla quantità di lavoro svolto dalle pipeline di elaborazione dell'intera query. Quantità di lavoro viene eseguita per documenti di grandi dimensioni, pertanto è necessari per caricare ed elaborare i documenti di grandi dimensioni più tempo e russo.

## <a name="low-provisioned-throughput"></a>Velocità effettiva con provisioning insufficiente
Garantire velocità effettiva con provisioning può gestire il carico di lavoro. Aumentare il budget di UR per le raccolte interessate.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Provare ad aggiornare alla versione più recente del SDK
Per determinare il SDK più recente, vedere [note sulla versione e di Download SDK](sql-api-sdk-dotnet.md) articolo.

## <a name="next-steps"></a>Passaggi successivi
Fare riferimento ai documenti seguenti su come misura di unità riservate per ogni query, ottenere le statistiche di esecuzione per ottimizzare le query e altro ancora:

* [Ottenere metriche di esecuzione delle query SQL tramite .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)