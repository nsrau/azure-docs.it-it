---
title: Risolvere i problemi di query quando si usa Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi Azure Cosmos DB query SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0dd3cb12c52e23a0a8acd57bf401ba68acfb9925
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623699"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Risolvere i problemi di query quando si usa Azure Cosmos DB

Questo articolo illustra un approccio generale consigliato per la risoluzione dei problemi relativi alle query in Azure Cosmos DB. Mentre i passaggi descritti in questo documento non devono essere considerati "catch all" per potenziali problemi di query, sono stati inclusi i suggerimenti per le prestazioni più comuni qui. È consigliabile usare questo documento come punto di partenza per la risoluzione dei problemi relativi alle query lente o costose nell'API principale (SQL) di Azure Cosmos DB. È anche possibile usare i [log di diagnostica](cosmosdb-monitor-resource-logs.md) per identificare le query lente o utilizzare quantità significative di velocità effettiva.

È possibile suddividere in categorie le ottimizzazioni delle query in Azure Cosmos DB: ottimizzazioni che riducono l'addebito delle unità richiesta (UR) per la query e le ottimizzazioni che riducono solo la latenza. Riducendo il costo delle UR di una query, si ridurrà quasi certamente la latenza.

In questo documento vengono usati esempi che possono essere ricreati usando il set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

## <a name="important"></a>Importante

- Per ottenere prestazioni ottimali, seguire i suggerimenti per le [prestazioni](performance-tips.md).
    > [!NOTE] 
    > Per migliorare le prestazioni, è consigliabile usare l'elaborazione host Windows a 64 bit. SQL SDK include un file ServiceInterop. dll nativo per analizzare e ottimizzare le query in locale ed è supportato solo nella piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop. dll non è disponibile, effettuerà una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata. 
- Cosmos DB query non supporta il numero minimo di elementi.
    - Il codice deve gestire qualsiasi dimensione di pagina da 0 a numero massimo di elementi
    - Il numero di elementi in una pagina può essere modificato senza preavviso.
- Le pagine vuote sono previste per le query e possono essere visualizzate in qualsiasi momento. 
    - Il motivo per cui le pagine vuote sono esposte negli SDK è che consente più opportunità di annullare la query. Consente inoltre di chiarire che l'SDK sta eseguendo più chiamate di rete.
    - Le pagine vuote possono essere visualizzate nei carichi di lavoro esistenti perché una partizione fisica è divisa in Cosmos DB. Per la prima partizione sono ora presenti 0 risultati, che causano la pagina vuota.
    - Le pagine vuote sono causate dal back-end che precede la query perché la query sta impiegando più di una quantità di tempo fissa nel back-end per recuperare i documenti. Se Cosmos DB precede una query, restituirà un token di continuazione che consentirà alla query di continuare. 
- Verificare che la query venga svuotata completamente. Esaminare gli esempi di SDK e usare un ciclo while sul `FeedIterator.HasMoreResults` per svuotare l'intera query.

### <a name="obtaining-query-metrics"></a>Ottenere le metriche della query:

Quando si ottimizza una query in Azure Cosmos DB, il primo passaggio consiste sempre nell' [ottenere le metriche della query](profile-sql-api-query.md) per la query. Sono disponibili anche tramite il portale di Azure come illustrato di seguito:

[![ottenere le metriche di query](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Dopo aver ottenuto le metriche della query, confrontare il numero di documenti recuperati con il numero di documenti di output per la query. Utilizzare questo confronto per identificare le sezioni rilevanti a cui fare riferimento.

Il conteggio del documento recuperato è il numero di documenti necessari per il caricamento della query. Il conteggio dei documenti di output è il numero di documenti necessari per i risultati della query. Se il conteggio dei documenti recuperati è significativamente superiore rispetto al numero di documenti di output, è presente almeno una parte della query che non è in grado di utilizzare l'indice ed è necessario eseguire un'analisi.

È possibile fare riferimento alla sezione seguente per comprendere le ottimizzazioni delle query pertinenti per lo scenario:

### <a name="querys-ru-charge-is-too-high"></a>L'addebito delle UR della query è troppo elevato

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Il conteggio dei documenti recuperati è significativamente maggiore del numero di documenti di output

- [Includere i percorsi necessari nei criteri di indicizzazione](#include-necessary-paths-in-the-indexing-policy)

- [Informazioni sulle funzioni di sistema che utilizzano l'indice](#understand-which-system-functions-utilize-the-index)

- [Query con una clausola Filter e ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

- [Ottimizzare le espressioni di JOIN utilizzando una sottoquery](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Il conteggio del documento recuperato è approssimativamente uguale al numero di documenti di output

- [Evitare query tra partizioni](#avoid-cross-partition-queries)

- [Filtri su più proprietà](#filters-on-multiple-properties)

- [Query con una clausola Filter e ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>L'addebito delle UR della query è accettabile, ma la latenza è ancora troppo elevata

- [Miglioramento della prossimità](#improve-proximity)

- [Aumenta la velocità effettiva con provisioning](#increase-provisioned-throughput)

- [Aumenta MaxConcurrency](#increase-maxconcurrency)

- [Aumenta MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query in cui il numero di documenti recuperati supera il numero di documenti di output

 Il conteggio del documento recuperato è il numero di documenti necessari per il caricamento della query. Il conteggio dei documenti di output è il numero di documenti necessari per i risultati della query. Se il conteggio dei documenti recuperati è significativamente superiore rispetto al numero di documenti di output, è presente almeno una parte della query che non è in grado di utilizzare l'indice ed è necessario eseguire un'analisi.

 Di seguito è riportato un esempio di query di analisi che non è stata interamente gestita dall'indice.

Query:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metriche della query:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Il numero di documenti recuperati (60.951) è significativamente maggiore del numero di documenti di output (7), quindi questa query ha richiesto di eseguire un'analisi. In questo caso, la funzione di sistema [Upper ()](sql-query-upper.md) non usa l'indice.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Includere i percorsi necessari nei criteri di indicizzazione

I criteri di indicizzazione devono coprire tutte le proprietà incluse in `WHERE` clausole, clausole `ORDER BY`, `JOIN`e la maggior parte delle funzioni di sistema. Il percorso specificato nei criteri di indice deve corrispondere (maiuscole/minuscole) alla proprietà nei documenti JSON.

Se si esegue una query semplice sul set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) , si osserva un addebito di ur molto inferiore quando la proprietà nella clausola `WHERE` viene indicizzata.

### <a name="original"></a>Originale

Query:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Criteri di indicizzazione:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Costo ur:** 409,51 ur

### <a name="optimized"></a>Ottimizzato

Criteri di indicizzazione aggiornati:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Costo ur:** 2,98 UR

È possibile aggiungere altre proprietà ai criteri di indicizzazione in qualsiasi momento, senza alcun effetto sulla disponibilità o sulle prestazioni di scrittura. Se si aggiunge una nuova proprietà all'indice, le query che utilizzano questa proprietà utilizzeranno immediatamente il nuovo indice disponibile. Per la query verrà utilizzato il nuovo indice durante la compilazione. Di conseguenza, i risultati della query potrebbero non essere coerenti perché è in corso la ricompilazione dell'indice. Se viene indicizzata una nuova proprietà, le query che utilizzano solo gli indici esistenti non saranno interessate dalla ricompilazione dell'indice. È possibile [monitorare lo stato di avanzamento della trasformazione dell'indice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Informazioni sulle funzioni di sistema che utilizzano l'indice

Se l'espressione può essere convertita in un intervallo di valori stringa, può utilizzare l'indice; in caso contrario, non può.

Di seguito è riportato l'elenco delle funzioni stringa che possono utilizzare l'indice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ma solo se il primo num_expr è 0

Di seguito sono riportate alcune funzioni di sistema comuni che non utilizzano l'indice e che devono caricare ciascun documento:

| **Funzione di sistema**                     | **Idee per l'ottimizzazione**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Usare ricerca di Azure per la ricerca full-text                        |
| SUPERIORE/INFERIORE                             | Anziché utilizzare la funzione di sistema per normalizzare i dati ogni volta per i confronti, è necessario normalizzare la combinazione di maiuscole e minuscole dopo l'inserimento. Una query come ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` diventa semplicemente ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Funzioni matematiche (non aggregate) | Se è necessario calcolare spesso un valore nella query, è consigliabile archiviare questo valore come una proprietà nel documento JSON. |

------

Altre parti della query possono comunque utilizzare l'indice nonostante le funzioni di sistema non utilizzino l'indice.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Query con una clausola Filter e ORDER BY

Sebbene le query con un filtro e una clausola `ORDER BY` utilizzeranno normalmente un indice di intervallo, saranno più efficienti se possono essere gestite da un indice composto. Oltre a modificare i criteri di indicizzazione, è necessario aggiungere tutte le proprietà nell'indice composito alla clausola `ORDER BY`. Questa modifica della query garantisce che venga utilizzato l'indice composto.  È possibile osservare l'effetto eseguendo una query sul set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

### <a name="original"></a>Originale

Query:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Criteri di indicizzazione:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Costo ur:** 44,28 ur

### <a name="optimized"></a>Ottimizzato

Query aggiornata (include entrambe le proprietà nella clausola `ORDER BY`):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Criteri di indicizzazione aggiornati:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Costo ur:** 8,86 ur

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Ottimizzare le espressioni di JOIN utilizzando una sottoquery
Le sottoquery multivalore consentono di ottimizzare le espressioni `JOIN` eseguendo il push dei predicati dopo ogni espressione SELECT-many invece che dopo tutti i cross join nella clausola `WHERE`.

Si consideri la query seguente:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Costo ur:** 167,62 ur

Per questa query, l'indice corrisponderà a qualsiasi documento che contiene un tag con il nome "Infant formula", nutritionValue maggiore di 0 e una quantità maggiore di 1. L'espressione `JOIN` eseguirà il prodotto incrociato di tutti gli elementi di tag, nutrienti e servirà matrici per ogni documento corrispondente prima dell'applicazione di qualsiasi filtro. La clausola `WHERE` applicherà quindi il predicato del filtro per ogni tupla di `<c, t, n, s>`.

Se, ad esempio, un documento corrispondente include 10 elementi in ognuna delle tre matrici, si espanderà fino a 1 x 10 x 10 x 10 (ovvero 1.000) Tuple. L'uso di sottoquery consente di filtrare gli elementi della matrice aggiunti prima di unirli all'espressione successiva.

Questa query è equivalente a quella precedente, ma usa sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Costo ur:** 22,17 ur

Si supponga che un solo elemento nella matrice dei tag corrisponda al filtro e che siano disponibili cinque elementi sia per i nutrienti che per la conservazione delle matrici. Le espressioni `JOIN` si espanderanno quindi fino a 1 x 1 x 5 x 5 = 25 elementi, anziché 1.000 elementi nella prima query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query in cui il numero di documenti recuperati è uguale al numero di documenti di output

Se il conteggio dei documenti recuperati è approssimativamente uguale al numero di documenti di output, significa che la query non ha dovuto analizzare molti documenti superflui. Per molte query, ad esempio quelle che usano la parola chiave TOP, il conteggio dei documenti recuperati può superare il numero di documenti di output di 1. Questa operazione non deve essere motivo di preoccupazione.

## <a name="avoid-cross-partition-queries"></a>Evitare query tra partizioni

Azure Cosmos DB usa il [partizionamento](partitioning-overview.md) per ridimensionare i singoli contenitori, in quanto le unità richiesta e le necessità di archiviazione dati aumentano. Ogni partizione fisica dispone di un indice separato e indipendente. Se la query ha un filtro di uguaglianza che corrisponde alla chiave di partizione del contenitore, sarà sufficiente controllare l'indice della partizione pertinente. Questa ottimizzazione riduce il numero totale di ur richieste dalla query.

Se si dispone di un numero elevato di UR di cui è stato effettuato il provisioning (oltre 30.000) o di una grande quantità di dati archiviati (oltre circa 100 GB), è probabile che si disponga di un contenitore sufficientemente grande per vedere una riduzione significativa degli addebiti per le unità richiesta.

Se, ad esempio, si crea un contenitore con la chiave di partizione foodGroup, le query seguenti dovranno solo controllare una singola partizione fisica:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Queste query vengono inoltre ottimizzate includendo la chiave di partizione nella query:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Per le query che includono filtri di intervallo sulla chiave di partizione o che non hanno filtri sulla chiave di partizione, sarà necessario "Smazzare" e controllare l'indice di ogni partizione fisica per ottenere risultati.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filtri su più proprietà

Sebbene le query con filtri su più proprietà utilizzeranno normalmente un indice di intervallo, saranno più efficienti se possono essere gestite da un indice composto. Per piccole quantità di dati, questa ottimizzazione non avrà un impatto significativo. Tuttavia, può rivelarsi utile per grandi quantità di dati. È possibile ottimizzare, al massimo, un filtro di non uguaglianza per ogni indice composto. Se nella query sono presenti più filtri di non uguaglianza, è necessario sceglierne uno che utilizzerà l'indice composto. Il resto continuerà a usare gli indici di intervallo. Il filtro di non uguaglianza deve essere definito per ultimo nell'indice composto. [Altre informazioni sugli indici compositi](index-policy.md#composite-indexes)

Di seguito sono riportati alcuni esempi di query che possono essere ottimizzate con un indice composito:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Di seguito è riportato l'indice composito pertinente:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Ottimizzazioni che riducono la latenza delle query:

In molti casi, l'addebito delle UR può essere accettabile, ma la latenza delle query è ancora troppo elevata. Le sezioni seguenti offrono una panoramica dei suggerimenti per la riduzione della latenza delle query. Se la stessa query viene eseguita più volte nello stesso set di dati, lo stesso addebito viene addebitato ogni volta. Tuttavia, la latenza delle query può variare tra le esecuzioni di query.

## <a name="improve-proximity"></a>Miglioramento della prossimità

Le query che vengono eseguite da un'area diversa da quella dell'account Azure Cosmos DB avranno una latenza superiore rispetto a quando vengono eseguite all'interno della stessa area. Se, ad esempio, si esegue il codice sul computer desktop, è necessario prevedere che la latenza sia di decine o centinaia (o più) millisecondi superiore a se la query proviene da una macchina virtuale all'interno della stessa area di Azure come Azure Cosmos DB. È semplice distribuire a [livello globale i dati in Azure Cosmos DB](distribute-data-globally.md) per assicurarsi che sia possibile avvicinare i dati all'app.

## <a name="increase-provisioned-throughput"></a>Aumenta la velocità effettiva con provisioning

In Azure Cosmos DB la velocità effettiva con provisioning viene misurata in unità richiesta (UR). Si supponga di disporre di una query che utilizza 5 UR di velocità effettiva. Se, ad esempio, si esegue il provisioning di 1.000 UR, sarà possibile eseguire la query 200 volte al secondo. Se si è tentato di eseguire la query quando la velocità effettiva disponibile non è sufficiente, Azure Cosmos DB restituirebbe un errore HTTP 429. Uno qualsiasi degli SDK dell'API Core (SQL) corrente tenterà automaticamente di ripetere la query dopo un breve periodo di tempo. Le richieste limitate richiedono una quantità di tempo maggiore, quindi l'aumento della velocità effettiva con provisioning può migliorare la latenza delle query. È possibile osservare il [numero totale di richieste limitate](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) nel pannello metriche del portale di Azure.

## <a name="increase-maxconcurrency"></a>Aumenta MaxConcurrency

Le query parallele funzionano eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. Quindi, la modifica di MaxConcurrency al numero di partizioni ha la possibilità massima di ottenere la query più efficiente, purché tutte le altre condizioni del sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare MaxConcurrency (o MaxDegreesOfParallelism nelle versioni precedenti dell'SDK) su un numero elevato e il sistema sceglie il numero minimo di partizioni, ovvero l'input fornito dall'utente, come massimo grado di parallelismo.

## <a name="increase-maxbuffereditemcount"></a>Aumenta MaxBufferedItemCount

Le query sono progettate per recuperare in anticipo i risultati mentre il batch di risultati corrente viene elaborato dal client. La prelettura consente il miglioramento complessivo della latenza di una query. L'impostazione di MaxBufferedItemCount limita il numero di risultati prerecuperati. Impostando questo valore sul numero previsto di risultati restituiti (o un numero più alto), la query può ricevere il massimo vantaggio dal recupero preliminare. Impostando questo valore su-1 si consente al sistema di decidere automaticamente il numero di elementi da memorizzare nel buffer.

## <a name="next-steps"></a>Passaggi successivi
Vedere i documenti riportati di seguito per informazioni su come misurare ur per query, ottenere statistiche di esecuzione per ottimizzare le query e altro ancora:

* [Ottenere le metriche di esecuzione delle query SQL usando .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)
