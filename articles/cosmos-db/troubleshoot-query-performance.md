---
title: Risolvere i problemi di query quando si usa Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi Azure Cosmos DB query SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b3c6926f17e8378fd3b53bfd59a7c5ea8141adb4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097235"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Risolvere i problemi di query quando si usa Azure Cosmos DB

Questo articolo illustra un approccio generale consigliato per la risoluzione dei problemi relativi alle query in Azure Cosmos DB. Anche se non è necessario prendere in considerazione i passaggi descritti in questo articolo come una difesa completa contro potenziali problemi di query, sono stati inclusi i suggerimenti sulle prestazioni più comuni. È consigliabile usare questo articolo come punto di partenza per la risoluzione dei problemi relativi alle query lente o costose nell'API Azure Cosmos DB Core (SQL). È inoltre possibile utilizzare i [log di diagnostica](cosmosdb-monitor-resource-logs.md) per identificare le query lente o che utilizzano quantità significative di velocità effettiva.

È possibile suddividere in categorie le ottimizzazioni delle query in Azure Cosmos DB:

- Ottimizzazioni che riducono il costo dell'unità richiesta (UR) della query
- Ottimizzazioni che riducono solo la latenza

Se si riduce l'addebito delle unità richiesta di una query, è quasi certamente possibile ridurre la latenza.

Questo articolo fornisce esempi che è possibile ricreare usando il set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

## <a name="common-sdk-issues"></a>Problemi comuni dell'SDK

Prima di leggere questa guida, è opportuno considerare i problemi comuni dell'SDK che non sono correlati al motore di query.

- Per ottenere prestazioni ottimali, seguire questi [suggerimenti sulle prestazioni](performance-tips.md).
    > [!NOTE]
    > Per migliorare le prestazioni, è consigliabile l'elaborazione host Windows a 64 bit. SQL SDK include un file ServiceInterop. dll nativo per analizzare e ottimizzare le query in locale. ServiceInterop. dll è supportato solo nella piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop. dll non è disponibile, viene effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata.
- L'SDK consente di impostare `MaxItemCount` un per le query, ma non è possibile specificare un numero minimo di elementi.
    - Il `MaxItemCount`codice deve gestire qualsiasi dimensione di pagina, da zero a.
    - Il numero di elementi in una pagina sarà sempre minore o uguale all'oggetto specificato `MaxItemCount`. Tuttavia, `MaxItemCount` è rigorosamente un valore massimo e potrebbe essere presente un minor numero di risultati rispetto a questa quantità.
- A volte le query possono avere pagine vuote anche quando sono presenti risultati in una pagina futura. I motivi possono essere i seguenti:
    - L'SDK potrebbe eseguire più chiamate di rete.
    - Il recupero dei documenti potrebbe richiedere molto tempo.
- Tutte le query hanno un token di continuazione che consentirà la continuazione della query. Assicurarsi di svuotare completamente la query. Esaminare gli esempi di SDK e usare un `while` ciclo in `FeedIterator.HasMoreResults` per svuotare l'intera query.

## <a name="get-query-metrics"></a>Ottenere le metriche di query

Quando si ottimizza una query in Azure Cosmos DB, il primo passaggio consiste sempre nell' [ottenere le metriche della query](profile-sql-api-query.md) per la query. Queste metriche sono disponibili anche tramite il portale di Azure. Una volta eseguita la query nel Esplora dati, le metriche della query sono visibili accanto alla scheda **risultati** :

[![Recupero di metriche](./media/troubleshoot-query-performance/obtain-query-metrics.png) di query](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Una volta recuperate le metriche della query, confrontare il **numero di documenti recuperati** con il **numero di documenti di output** per la query. Usare questo confronto per identificare le sezioni rilevanti da esaminare in questo articolo.

Il **conteggio del documento recuperato** è il numero di documenti necessari per il caricamento del motore di query. Il **conteggio** dei documenti di output è il numero di documenti necessari per i risultati della query. Se il **conteggio dei documenti recuperati** è significativamente superiore rispetto al **numero di documenti di output**, era presente almeno una parte della query che non è in grado di utilizzare un indice ed è necessario eseguire un'analisi.

Per informazioni sulle ottimizzazioni delle query pertinenti per lo scenario, vedere le sezioni seguenti.

### <a name="querys-ru-charge-is-too-high"></a>L'addebito delle UR della query è troppo elevato

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Il numero di documenti recuperati è significativamente superiore al numero di documenti di output

- [Includere i percorsi necessari nei criteri di indicizzazione.](#include-necessary-paths-in-the-indexing-policy)

- [Informazioni sulle funzioni di sistema che utilizzano l'indice.](#understand-which-system-functions-use-the-index)

- [Informazioni sulle query di aggregazione che utilizzano l'indice.](#understand-which-aggregate-queries-use-the-index)

- [Ottimizzare le query con un filtro e una clausola ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Ottimizzare le espressioni di JOIN utilizzando una sottoquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Il conteggio del documento recuperato è approssimativamente uguale al numero di documenti di output

- [Ridurre al minimo le query tra partizioni.](#minimize-cross-partition-queries)

- [Ottimizzare le query con filtri su più proprietà.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Ottimizzare le query con un filtro e una clausola ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>L'addebito delle UR della query è accettabile, ma la latenza è ancora troppo elevata

- [Miglioramento della prossimità.](#improve-proximity)

- [Aumentare la velocità effettiva con provisioning.](#increase-provisioned-throughput)

- [Aumentare MaxConcurrency.](#increase-maxconcurrency)

- [Aumentare MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query in cui il numero di documenti recuperati supera il numero di documenti di output

 Il **conteggio del documento recuperato** è il numero di documenti necessari per il caricamento del motore di query. Il **conteggio** dei documenti di output è il numero di documenti restituiti dalla query. Se il **conteggio dei documenti recuperati** è significativamente superiore rispetto al **numero di documenti di output**, era presente almeno una parte della query che non è in grado di utilizzare un indice ed è necessario eseguire un'analisi.

Di seguito è riportato un esempio di query di analisi che non è stata interamente gestita dall'indice:

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

Il **numero di documenti recuperati** (60.951) è significativamente superiore al **numero di documenti di output** (7), che implica che la query ha generato un'analisi del documento. In questo caso, la funzione di sistema [Upper ()](sql-query-upper.md) non utilizza un indice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Includere i percorsi necessari nei criteri di indicizzazione

I criteri di indicizzazione dovrebbero coprire le proprietà `WHERE` incluse nelle clausole, `ORDER BY` nelle `JOIN`clausole, e nella maggior parte delle funzioni di sistema. I percorsi desiderati specificati nei criteri di indice devono corrispondere alle proprietà nei documenti JSON.

> [!NOTE]
> Le proprietà nei criteri di indicizzazione Azure Cosmos DB fanno distinzione tra maiuscole e minuscole

Se si esegue la query semplice seguente sul set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) , si noterà un addebito delle UR molto più basso quando `WHERE` viene indicizzata la proprietà nella clausola:

#### <a name="original"></a>Originale

Query:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

#### <a name="optimized"></a>Ottimizzato

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

È possibile aggiungere proprietà ai criteri di indicizzazione in qualsiasi momento, senza alcun effetto sulla disponibilità o sulle prestazioni di scrittura. Se si aggiunge una nuova proprietà all'indice, le query che utilizzano la proprietà utilizzeranno immediatamente il nuovo indice disponibile. La query utilizzerà il nuovo indice mentre viene compilato. I risultati delle query potrebbero quindi risultare incoerenti mentre è in corso la ricompilazione dell'indice. Se viene indicizzata una nuova proprietà, le query che utilizzano solo gli indici esistenti non saranno interessate durante la ricompilazione dell'indice. È possibile [monitorare lo stato di avanzamento della trasformazione dell'indice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Informazioni sulle funzioni di sistema che usano l'indice

Se un'espressione può essere convertita in un intervallo di valori stringa, può usare l'indice. In caso contrario, non può.

Ecco l'elenco di alcune funzioni stringa comuni che possono usare l'indice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- Substring (str_expr, num_expr, num_expr) = str_expr, ma solo se il primo num_expr è 0

Di seguito sono riportate alcune funzioni di sistema comuni che non utilizzano l'indice e che devono caricare ciascun documento:

| **Funzione di sistema**                     | **Idee per l'ottimizzazione**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Usare ricerca di Azure per la ricerca full-text.                        |
| SUPERIORE/INFERIORE                             | Anziché utilizzare la funzione di sistema per normalizzare i dati per i confronti, normalizzare l'involucro al momento dell'inserimento. Una query come ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` diventa ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| Funzioni matematiche (non aggregate) | Se è necessario calcolare un valore di frequente nella query, è consigliabile archiviare il valore come proprietà nel documento JSON. |

------

Altre parti della query potrebbero ancora usare l'indice anche se le funzioni di sistema non lo sono.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Informazioni sulle query di aggregazione che usano l'indice

Nella maggior parte dei casi, le funzioni di sistema di aggregazione in Azure Cosmos DB utilizzeranno l'indice. Tuttavia, a seconda dei filtri o delle clausole aggiuntive in una query di aggregazione, il motore di query potrebbe essere necessario per caricare un numero elevato di documenti. Il motore di query, in genere, applica prima i filtri di uguaglianza e di intervallo. Dopo aver applicato questi filtri, il motore di query può valutare filtri aggiuntivi e ricorrere al caricamento dei documenti rimanenti per calcolare l'aggregazione, se necessario.

Ad esempio, date queste due query di esempio, la query con un filtro di `CONTAINS` uguaglianza e funzione di sistema sarà in genere più efficiente rispetto a una query `CONTAINS` con un solo filtro di funzione di sistema. Questo è dovuto al fatto che il filtro di uguaglianza viene applicato per primo e utilizza l'indice prima che i documenti debbano essere caricati per il filtro più costoso `CONTAINS` .

Esecuzione di query `CONTAINS` con solo filtro-costo ur superiore:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Query con filtro di uguaglianza e `CONTAINS` filtro-addebito per le UR inferiore:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Di seguito sono riportati altri esempi di query di aggregazione che non utilizzeranno completamente l'indice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Query con funzioni di sistema che non utilizzano l'indice

È necessario fare riferimento alla [pagina della funzione di sistema](sql-query-system-functions.md) pertinente per verificare se viene utilizzato l'indice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Aggregare query con funzioni definite dall'utente (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Query con GROUP BY

L'addebito delle unità richiesta `GROUP BY` per le query con aumenterà man mano che aumenta la `GROUP BY` cardinalità delle proprietà nella clausola. Nella query seguente, ad esempio, l'addebito delle UR della query aumenterà man mano che aumenta il numero di descrizioni univoche.

L'addebito delle UR di una funzione di `GROUP BY` aggregazione con una clausola sarà superiore rispetto a quello di una funzione di aggregazione. In questo esempio, il motore di query deve caricare tutti i documenti che `c.foodGroup = "Sausages and Luncheon Meats"` corrispondono al filtro in modo che l'addebito delle UR sia elevato.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Se si prevede di eseguire frequentemente le stesse query di aggregazione, potrebbe essere più efficiente compilare una vista materializzata in tempo reale con il [Azure Cosmos DB feed di modifiche](change-feed.md) rispetto all'esecuzione di singole query.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Ottimizzare le query con una clausola Filter e ORDER BY

Sebbene le query con un filtro e una `ORDER BY` clausola usino normalmente un indice di intervallo, saranno più efficienti se possono essere gestite da un indice composto. Oltre a modificare i criteri di indicizzazione, è necessario aggiungere tutte le proprietà nell'indice composito `ORDER BY` alla clausola. Questa modifica alla query garantisce che venga utilizzato l'indice composto.  È possibile osservare l'effetto eseguendo una query sul set di dati [nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) :

#### <a name="original"></a>Originale

Query:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

#### <a name="optimized"></a>Ottimizzato

Query aggiornata (include entrambe le `ORDER BY` proprietà nella clausola):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
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

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Ottimizzare le espressioni di JOIN utilizzando una sottoquery

Le sottoquery multivalore consentono di ottimizzare `JOIN` le espressioni eseguendo il `WHERE` push dei predicati dopo ogni espressione SELECT-many invece che dopo tutti i cross join nella clausola.

Considerare la query seguente:

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

Per questa query, l'indice corrisponderà a qualsiasi documento con un tag con il nome `infant formula`, `nutritionValue` maggiore di 0 e `amount` maggiore di 1. Qui `JOIN` l'espressione eseguirà il prodotto incrociato di tutti gli elementi di tag, nutrienti e servirà matrici per ogni documento corrispondente prima dell'applicazione di qualsiasi filtro. La `WHERE` clausola applicherà quindi il predicato `<c, t, n, s>` del filtro in ogni tupla.

Se, ad esempio, un documento corrispondente contiene 10 elementi in ognuna delle tre matrici, si espanderà fino a 1 x 10 x 10 x 10 (ovvero 1.000) Tuple. L'uso di sottoquery qui può consentire di filtrare gli elementi della matrice aggiunti prima di unirli all'espressione successiva.

Questa query è equivalente a quella precedente, ma usa sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Costo ur:** 22,17 ur

Si supponga che un solo elemento nella matrice dei tag corrisponda al filtro e che siano presenti cinque elementi per le sostanze nutrienti e per la conservazione delle matrici. Le `JOIN` espressioni si espanderanno fino a 1 x 1 x 5 x 5 = 25 elementi, anziché 1.000 elementi nella prima query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query in cui il numero di documenti recuperati è uguale al numero di documenti di output

Se il **conteggio del documento recuperato** è approssimativamente uguale al **numero**di documenti di output, il motore di query non ha dovuto analizzare molti documenti superflui. Per molte query, come quelle che usano la `TOP` parola chiave, il **conteggio dei documenti recuperati** può superare il **numero di documenti di output** di 1. Non è necessario preoccuparsi di questa operazione.

### <a name="minimize-cross-partition-queries"></a>Ridurre al minimo le query tra partizioni

Azure Cosmos DB usa il [partizionamento](partitioning-overview.md) per ridimensionare i singoli contenitori, in quanto le unità richiesta e le necessità di archiviazione dati aumentano. Ogni partizione fisica dispone di un indice separato e indipendente. Se la query ha un filtro di uguaglianza che corrisponde alla chiave di partizione del contenitore, è necessario controllare solo l'indice della partizione pertinente. Questa ottimizzazione riduce il numero totale di ur richieste dalla query.

Se si dispone di un numero elevato di UR di cui è stato effettuato il provisioning (più di 30.000) o di una grande quantità di dati archiviati (oltre circa 100 GB), è probabile che si disponga di un contenitore sufficientemente grande per vedere una riduzione significativa degli addebiti per le unità richiesta.

Se ad esempio si crea un contenitore con la chiave di partizione foodGroup, le query seguenti dovranno controllare solo una singola partizione fisica:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Le query che hanno `IN` un filtro con la chiave di partizione verificheranno solo le partizioni fisiche rilevanti e non "fan-out":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Le query con filtri di intervallo sulla chiave di partizione o che non hanno filtri sulla chiave di partizione devono essere "fan-out" e controllare l'indice di ogni partizione fisica per i risultati:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Ottimizzare le query con filtri su più proprietà

Sebbene le query con filtri su più proprietà utilizzeranno normalmente un indice di intervallo, saranno più efficienti se possono essere gestite da un indice composto. Per piccole quantità di dati, questa ottimizzazione non avrà un impatto significativo. Può tuttavia essere utile per grandi quantità di dati. È possibile ottimizzare, al massimo, un filtro di non uguaglianza per ogni indice composto. Se nella query sono presenti più filtri di non uguaglianza, selezionarne uno che utilizzerà l'indice composto. Rest continuerà a usare gli indici di intervallo. Il filtro di non uguaglianza deve essere definito per ultimo nell'indice composto. [Altre informazioni sugli indici compositi](index-policy.md#composite-indexes).

Di seguito sono riportati alcuni esempi di query che possono essere ottimizzate con un indice composito:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>Ottimizzazioni che riducono la latenza delle query

In molti casi, l'addebito delle UR potrebbe essere accettabile quando la latenza delle query è ancora troppo elevata. Le sezioni seguenti forniscono una panoramica dei suggerimenti per la riduzione della latenza delle query. Se la stessa query viene eseguita più volte nello stesso set di dati, lo stesso addebito viene addebitato ogni volta. La latenza delle query può tuttavia variare tra le esecuzioni di query.

### <a name="improve-proximity"></a>Miglioramento della prossimità

Le query eseguite da un'area diversa da quella dell'account di Azure Cosmos DB avranno una latenza superiore rispetto a quando vengono eseguite all'interno della stessa area. Se, ad esempio, si esegue il codice sul computer desktop, è necessario prevedere che la latenza sia di decine o centinaia di millisecondi (o più) rispetto al caso in cui la query provenga da una macchina virtuale all'interno della stessa area di Azure come Azure Cosmos DB. È semplice distribuire a [livello globale i dati in Azure Cosmos DB](distribute-data-globally.md) per assicurarsi di poter avvicinare i dati all'app.

### <a name="increase-provisioned-throughput"></a>Aumenta la velocità effettiva con provisioning

In Azure Cosmos DB la velocità effettiva con provisioning viene misurata in unità richiesta (UR). Si supponga di disporre di una query che utilizza 5 UR di velocità effettiva. Se, ad esempio, si esegue il provisioning di 1.000 UR, sarà possibile eseguire la query 200 volte al secondo. Se si è tentato di eseguire la query quando la velocità effettiva disponibile non è sufficiente, Azure Cosmos DB restituirebbe un errore HTTP 429. Uno degli SDK di API Core (SQL) correnti tenterà automaticamente di eseguire questa query dopo l'attesa di un breve intervallo di tempo. Le richieste limitate richiedono più tempo, quindi l'aumento della velocità effettiva con provisioning può migliorare la latenza delle query. È possibile osservare il [numero totale di richieste limitate](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) nel pannello **metriche** del portale di Azure.

### <a name="increase-maxconcurrency"></a>Aumenta MaxConcurrency

Le query parallele funzionano eseguendo query su più partizioni in parallelo. Tuttavia, i dati di una singola raccolta partizionata vengono recuperati in modo seriale rispetto alla query. Quindi, se si imposta MaxConcurrency sul numero di partizioni, si ha la possibilità di ottenere la query più efficiente, purché tutte le altre condizioni del sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare un numero elevato per MaxConcurrency (o MaxDegreesOfParallelism nelle versioni precedenti dell'SDK). Il sistema sceglierà il numero minimo di partizioni, ovvero l'input fornito dall'utente, come massimo grado di parallelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumenta MaxBufferedItemCount

Le query sono progettate per recuperare in anticipo i risultati mentre il batch di risultati corrente viene elaborato dal client. Il recupero preliminare contribuisce a migliorare la latenza complessiva di una query. L'impostazione di MaxBufferedItemCount limita il numero di risultati prerecuperati. Se si imposta questo valore sul numero previsto di risultati restituiti (o un numero più elevato), la query può ottenere il massimo vantaggio dal recupero preliminare. Se si imposta questo valore su-1, il sistema determina automaticamente il numero di elementi da memorizzare nel buffer.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni su come misurare ur per query, ottenere statistiche di esecuzione per ottimizzare le query e altro ancora:

* [Ottenere le metriche di esecuzione delle query SQL tramite .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)
