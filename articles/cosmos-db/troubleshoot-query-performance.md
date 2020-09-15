---
title: Risolvere i problemi di query relativi all'uso di Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi delle query SQL in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 09/12/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: a6833f9d59eca4c2f0b49dd70684ade900226aba
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089990"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Risolvere i problemi di query relativi all'uso di Azure Cosmos DB

Questo articolo illustra un approccio generale consigliato per la risoluzione dei problemi relativi alle query in Azure Cosmos DB. I passaggi descritti in questo articolo non rappresentano una soluzione completa ai potenziali problemi di query, ma offrono suggerimenti per la soluzione degli errori più comuni relativi alle prestazioni. È consigliabile usare questo articolo come punto di partenza per la soluzione dei problemi relativi a query lente o con costo elevato nell'API Core (SQL) di Azure Cosmos DB. È inoltre possibile usare i [log di diagnostica](cosmosdb-monitor-resource-logs.md) per identificare le query lente o che usano quantità significative di velocità effettiva.

In generale, l'ottimizzazione delle query in Azure Cosmos DB può essere classificata in queste categorie:

- Ottimizzazioni che consentono di ridurre l'addebito dell'unità richiesta (UR) della query
- Ottimizzazioni che consentono di ridurre solo la latenza

Se si riduce l'addebito UR di una query, quasi sicuramente è possibile ridurre anche la latenza.

Questo articolo fornisce esempi che è possibile ricreare usando il set di [dati nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Problemi comuni dell'SDK

Prima di leggere questa guida, è opportuno prendere in considerazione i più problemi comuni dell'SDK che non sono correlati al motore di query.

- Seguire questi [suggerimenti sulle prestazioni dell'SDK](performance-tips.md).
    - [Guida alla risoluzione dei problemi di .NET SDK](troubleshoot-dot-net-sdk.md)
    - [Guida alla risoluzione dei problemi di Java SDK](troubleshoot-java-sdk-v4-sql.md)
- L'SDK consente di impostare un valore `MaxItemCount` per le query, ma non è possibile specificare un numero minimo di elementi.
    - Il codice deve gestire qualsiasi dimensione di pagina, da zero fino al valore`MaxItemCount`.
- A volte le query possono includere pagine vuote anche quando sono presenti risultati in una pagina successiva. Alcuni possibili motivi sono:
    - L'SDK sta eseguendo più chiamate di rete.
    - Il recupero dei documenti da parte della query potrebbe richiedere molto tempo.
- Tutte le query hanno un token di continuazione che consente la continuazione della query. Assicurarsi di svuotare completamente la query. Altre informazioni sulla [gestione di più pagine di risultati](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Recuperare le metriche della query

Quando si ottimizza una query in Azure Cosmos DB, il primo passaggio consiste sempre nell'[recuperare le metriche](profile-sql-api-query.md) per la query. Le metriche sono disponibili anche tramite il portale di Azure. Una volta eseguita la query in Esplora dati, le metriche della query sono visibili accanto alla scheda dei **risultati**:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Recupero delle metriche della query" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Dopo aver recuperato le metriche della query, confrontare il **conteggio dei documenti recuperati** con il **conteggio dei documenti di output** per la query. Usare questo confronto per identificare le sezioni rilevanti da esaminare in questo articolo.

Il **conteggio dei documenti recuperati** corrisponde al numero di documenti che il motore di query doveva caricare. Il **conteggio dei documenti di output** corrisponde al numero di documenti che erano necessari per i risultati della query. Un **conteggio dei documenti recuperati** significativamente superiore al **conteggio dei documenti di output** indica che almeno una parte della query non ha potuto usare un indice e ha dovuto eseguire un'analisi.

Per informazioni sull'ottimizzazione delle query relative a ogni scenario, vedere le sezioni che seguono.

### <a name="querys-ru-charge-is-too-high"></a>L'addebito UR della query è troppo elevato

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Il conteggio dei documenti recuperati è significativamente superiore al conteggio dei documenti di output

- [Includere i percorsi necessari nei criteri di indicizzazione.](#include-necessary-paths-in-the-indexing-policy)

- [Individuare le funzioni di sistema che usano l'indice.](#understand-which-system-functions-use-the-index)

- [Individuare le query di aggregazione che usano l'indice.](#understand-which-aggregate-queries-use-the-index)

- [Ottimizzare le query che includono un filtro e una clausola ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Ottimizzare le espressioni JOIN usando una sottoquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Il conteggio documenti recuperati è approssimativamente uguale al conteggio documenti di output

- [Ridurre al minimo le query su più partizioni.](#minimize-cross-partition-queries)

- [Ottimizzare le query con filtri su più proprietà.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Ottimizzare le query che includono un filtro e una clausola ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>L'addebito UR della query è accettabile, ma la latenza è ancora troppo elevata

- [Migliorare la prossimità.](#improve-proximity)

- [Aumentare la velocità effettiva sottoposta a provisioning.](#increase-provisioned-throughput)

- [Aumentare il valore MaxConcurrency.](#increase-maxconcurrency)

- [Aumentare il valore MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query in cui il conteggio dei documenti recuperati supera il conteggio documenti di output

 Il **conteggio dei documenti recuperati** corrisponde al numero di documenti che il motore di query doveva caricare. Il **conteggio dei documenti di output** è il numero di documenti restituiti dalla query. Un **conteggio dei documenti recuperati** significativamente superiore al **conteggio dei documenti di output** indica che almeno una parte della query non ha potuto usare un indice e ha dovuto eseguire un'analisi.

Di seguito è riportato un esempio di una query di analisi che non è stata interamente gestita dall'indice:

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

Il **conteggio dei documenti recuperati** (60.951) è significativamente superiore al **conteggio dei documenti di output**  (7), pertanto la query ha determinato a un'analisi del documento. In questo caso, la funzione di sistema [UPPER ()](sql-query-upper.md) non usa un indice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Includere i percorsi necessari nei criteri di indicizzazione

I criteri di indicizzazione devono interessare tutte le proprietà incluse nelle clausole `WHERE`, `ORDER BY`, `JOIN` e nella maggior parte delle funzioni di sistema. I percorsi desiderati specificati nei criteri di indicizzazione devono corrispondere alle proprietà nei documenti JSON.

> [!NOTE]
> Le proprietà nei criteri di indicizzazione di Azure Cosmos DB fanno distinzione tra maiuscole e minuscole.

Se si esegue la query semplice riportata di seguito per il set di dati [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json), si può notare un addebito UR decisamente inferiore quando la proprietà nella clausola `WHERE` viene indicizzata:

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

**Addebito UR**: 409,51 UR

#### <a name="optimized"></a>Con ottimizzazione

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

**Addebito UR**: 2,98 UR

È possibile aggiungere proprietà ai criteri di indicizzazione in qualsiasi momento, senza alcun effetto sulle prestazioni o sulla disponibilità di scrittura. Se si aggiunge una nuova proprietà all'indice, le query che usano tale proprietà usano immediatamente il nuovo indice disponibile. La query usa il nuovo indice mentre viene compilato. Pertanto, durante la ricompilazione dell'indice, i risultati delle query potrebbero risultare incoerenti. Se viene indicizzata una nuova proprietà, le query che usano solo gli indici esistenti non saranno interessate durante la ricompilazione dell'indice. È possibile [tenere traccia dell'avanzamento della trasformazione dell'indice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Individuare le funzioni di sistema che usano l'indice

Se un'espressione può essere convertita in un intervallo di valori stringa, tale espressione può usare l'indice. In caso contrario, ciò non è possibile.

Di seguito è riportato l'elenco di alcune funzioni di stringa comuni che possono usare l'indice:

- STARTSWITH(str_expr1, str_expr2, bool_expr)  
- CONTAINS(str_expr, str_expr, bool_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ma solo se il primo num_expr è 0

Di seguito sono riportate alcune funzioni di sistema comuni che non usano l'indice e che devono caricare ogni documento:

| **Funzione di sistema**                     | **Suggerimenti per l'ottimizzazione**             |
| --------------------------------------- |------------------------------------------------------------ |
| UPPER/LOWER                             | Anziché usare la funzione di sistema per normalizzare i dati per i confronti, normalizzare le maiuscole e minuscole al momento dell'inserimento. Una query come ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` diventa ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| Funzioni matematiche (non aggregate) | Se nella query è necessario calcolare spesso un valore specifico, è consigliabile memorizzare il valore come proprietà nel documento JSON. |

------

Altre parti della query potrebbero usare comunque l'indice anche se le funzioni di sistema non lo fanno.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Individuare le query di aggregazione che usano l'indice

Nella maggior parte dei casi, le funzioni di sistema di aggregazione in Azure Cosmos DB usano l'indice. Tuttavia, a seconda dei filtri o delle clausole aggiuntive di una query di aggregazione, potrebbe essere necessario usare il motore di query per caricare un numero elevato di documenti. In genere, il motore di query applica prima i filtri di uguaglianza e di intervallo. Dopo aver applicato questi filtri, il motore di query può valutare filtri aggiuntivi e ricorrere al caricamento dei documenti rimanenti per calcolare l'aggregazione, se necessario.

Ad esempio, nel caso di queste due query esemplificative, la query con un filtro di uguaglianza e un filtro con funzione di sistema `CONTAINS` sarà in genere più efficiente rispetto a una query solo con filtro basato sulla funzione di sistema `CONTAINS`. Questo è dovuto al fatto che il filtro di uguaglianza viene applicato per primo e usa l'indice prima che i documenti debbano essere caricati per il filtro `CONTAINS` più costoso.

Query solo con il filtro `CONTAINS`, addebito UR superiore:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Query con filtro di uguaglianza e filtro `CONTAINS`, addebito UR inferiore:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Di seguito sono riportati altri esempi di query di aggregazione che non usano completamente l'indice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Query con funzioni di sistema che non usano l'indice

Fare riferimento alla pagina della [funzione di sistema pertinente](sql-query-system-functions.md) per verificare se usa l'indice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Query di aggregazione con funzioni definite dall'utente

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Query con clausola GROUP BY

L'addebito UR per le query con `GROUP BY` aumenterà man mano che la cardinalità delle proprietà nella clausola `GROUP BY` aumenta. Nella query seguente, ad esempio, l'addebito UR della query aumenta man mano che aumenta il numero di descrizioni univoche.

L'addebito UR di una funzione di aggregazione con una clausola `GROUP BY` è superiore a quello di una sola funzione di aggregazione. In questo esempio, il motore di query deve caricare tutti i documenti che corrispondono al filtro `c.foodGroup = "Sausages and Luncheon Meats"`, pertanto è prevedibile che l'addebito UR sia elevato.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Se si prevede di eseguire spesso le stesse query di aggregazione, anziché eseguire le singole query, una soluzione più efficace può essere la creazione di una vista materializzata in tempo reale con il [feed di modifiche di Azure Cosmos DB](change-feed.md).

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Ottimizzare le query con un filtro e una clausola ORDER BY

Sebbene le query con un filtro e una clausola `ORDER BY` usino in genere un indice di intervallo, la loro esecuzione sarà più efficiente se viene gestita tramite un indice composto. Oltre a modificare i criteri di indicizzazione, è necessario aggiungere alla clausola `ORDER BY` tutte le proprietà dell'indice composto. Questa modifica alla query garantisce che venga utilizzato l'indice composto.  Di seguito viene mostrato l'effetto dell'esecuzione di una query nel set di dati [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json):

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

**Addebito UR**: 44,28 RU

#### <a name="optimized"></a>Con ottimizzazione

Query aggiornata (include entrambe le proprietà nella clausola `ORDER BY`):

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

**Addebito UR**: 8,86 UR

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Ottimizzare le espressioni JOIN usando una sottoquery

Le sottoquery multivalore consentono di ottimizzare le espressioni `JOIN` eseguendo il push dei predicati dopo ogni espressione SelectMany, anziché dopo tutti i cross join nella clausola `WHERE`.

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

**Addebito UR**: 167,62 UR

Per questa query, l'indice determina la corrispondenza per qualsiasi documento con tag `infant formula`, `nutritionValue` maggiore di 0 e `amount` maggiore di 1. L'espressione `JOIN` esegue il prodotto incrociato per tutti gli elementi di tag, nutrienti e matrici di porzioni per ogni documento corrispondente prima dell'applicazione di qualsiasi filtro. La clausola `WHERE` applica quindi il predicato del filtro per ogni tupla di `<c, t, n, s>`.

Se, ad esempio, un documento con corrispondenza contiene 10 elementi in ognuna delle tre matrici, verranno espanse un massimo di 1 x 10 x 10 x 10 (ovvero 1.000) tuple. L'uso di sottoquery in questo caso consente di filtrare gli elementi della matrice aggiunti prima dell'unione con l'espressione successiva.

Questa query è equivalente alla precedente, ma usa le sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Addebito UR**: 22,17 RU

Si supponga che un solo elemento nella matrice di tag corrisponda al filtro e che siano presenti cinque elementi per le matrici nutrizionali e delle porzioni. Le espressioni `JOIN` si espanderanno fino a 1 x 1 x 5 x 5 = 25 elementi, anziché fino a 1.000 elementi come nella prima query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query in cui il conteggio dei documenti recuperati è uguale al conteggio dei documenti di output

Se il **conteggio dei documenti recuperati** è approssimativamente uguale al **conteggio di documenti di output**, il motore di query non ha dovuto analizzare molti documenti superflui. Per molte query, ad esempio quelle che usano la parola chiave `TOP`, il **conteggio dei documenti recuperati** potrebbe superare il **conteggio dei documenti di output** di 1 unità. Non è necessario preoccuparsi di questa opzione.

### <a name="minimize-cross-partition-queries"></a>Ridurre al minimo le query su più partizioni

Azure Cosmos DB usa il [partizionamento](partitioning-overview.md) per la scalabilità dei singoli contenitori quando si riscontra un aumento dell'UR e delle risorse di archiviazione dati necessarie. Ogni partizione fisica dispone di un indice distinto e indipendente. Se la query include un filtro di uguaglianza che corrisponde alla chiave di partizione del contenitore, è necessario controllare solo l'indice della partizione pertinente. Questa ottimizzazione consente di ridurre il numero totale di UR necessarie per la query.

Se è presente un numero elevato di UR di cui è stato effettuato il provisioning (più di 30.000) o di una quantità elevata di dati archiviati (circa oltre 100 GB), è probabile che si disponga di un contenitore sufficientemente grande da consentire una riduzione significativa degli addebiti UR.

Se ad esempio si crea un contenitore con la chiave di partizione foodGroup, le query seguenti dovranno controllare solo una singola partizione fisica:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Le query che hanno un filtro `IN` con la chiave di partizione verificheranno solo le partizioni fisiche pertinenti e non effettueranno il fan-out:

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Per le query con filtri di intervallo o senza filtri per la chiave di partizione, dovrà essere effettuato il fan-out e controllato l'indice di ogni partizione fisica per ottenere i risultati:

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

Sebbene le query con filtri per più proprietà usino in genere un indice di intervallo, la loro esecuzione sarà più efficiente se viene gestita tramite un indice composto. Per piccole quantità di dati, l'ottimizzazione non ha un impatto significativo. Tuttavia, nel caso di grandi quantità di dati, l'ottimizzazione può essere molto utile. È possibile ottimizzare, al massimo, un filtro di non uguaglianza per ogni indice composto. Se nella query sono presenti più filtri di non uguaglianza, selezionarne uno per l'utilizzo dell'indice composto. Gli altri filtri continueranno a utilizzare gli indici di intervallo. Il filtro non di uguaglianza deve essere definito come ultimo nell'indice composto. [Altre informazioni sugli indici composti](index-policy.md#composite-indexes).

Di seguito sono riportati alcuni esempi di query che possono essere ottimizzate con un indice composto:

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

Di seguito è riportato l'indice composto pertinente:

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

## <a name="optimizations-that-reduce-query-latency"></a>Ottimizzazioni che consentono di ridurre la latenza della query

In molti casi, l'addebito UR potrebbe essere accettabile quando la latenza della query è ancora troppo elevata. Le sezioni seguenti forniscono una panoramica dei suggerimenti per la riduzione della latenza delle query. Se la stessa query viene eseguita più volte nello stesso set di dati, lo stesso addebito UR viene applicato ogni volta. La latenza delle query potrebbe tuttavia variare tra le esecuzioni della query.

### <a name="improve-proximity"></a>Migliorare la prossimità

Le query eseguite da un'area diversa da quella dell'account di Azure Cosmos DB avranno una latenza superiore rispetto a quando vengono eseguite all'interno della stessa area. Se, ad esempio, se si esegue il codice sul computer desktop, è prevedibile che la latenza sia superiore di decine o centinaia di millisecondi (o più) rispetto a una query eseguita su una macchina virtuale all'interno della stessa area di Azure come Azure Cosmos DB. La [distribuzione a livello globale dei dati in Azure Cosmos DB](distribute-data-globally.md) è un'operazione semplice che consente di migliorare la prossimità dei dati all'app.

### <a name="increase-provisioned-throughput"></a>Aumentare la velocità effettiva sottoposta a provisioning

In Azure Cosmos DB la velocità effettiva con provisioning viene misurata in Unità Richieste (UR). Si supponga di disporre di una query che utilizza 5 UR di velocità effettiva. Se, ad esempio, si esegue il provisioning di 1.000 UR, sarà possibile eseguire la query 200 volte al secondo. Se si tenta di eseguire la query quando la velocità effettiva disponibile non è sufficiente, Azure Cosmos DB restituisce un errore HTTP 429. Uno degli SDK dell'API Core (SQL) correnti tenterà automaticamente di eseguire la query dopo l'attesa di un breve intervallo di tempo. Le richieste limitate richiedono più tempo, quindi l'aumento della velocità effettiva con provisioning può diminuire la latenza delle query. È possibile visualizzare il [numero totale di richieste limitate](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) nel pannello delle **metriche** del portale di Azure.

### <a name="increase-maxconcurrency"></a>Aumentare il valore MaxConcurrency

Le query parallele funzionano eseguendo in parallelo le query su più partizioni. Tuttavia, i dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. Pertanto, se si imposta MaxConcurrency sul numero di partizioni si ha la migliore probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare un numero elevato per MaxConcurrency (o per MaxDegreesOfParallelism nelle versioni precedenti dell'SDK). In questo caso, il sistema sceglierà il numero minimo di partizioni, ovvero l'input fornito dall'utente, come massimo grado di parallelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentare il valore MaxBufferedItemCount

Le query sono progettate per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. La prelettura consente di migliorare la latenza complessiva di una query. L'impostazione di MaxBufferedItemCount consente di limitare il numero di risultati di prelettura. Se si imposta questo valore sul numero previsto di risultati restituiti (o su un numero più elevato), la query può ottenere il massimo vantaggio dalla prelettura. Se si imposta questo valore su -1, il sistema determina automaticamente il numero di elementi da memorizzare nel buffer.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni sulla misurazione delle UR per le query, sul recupero delle statistiche di esecuzione per l'ottimizzazione e per altro ancora:

* [Ottenere le metriche di esecuzione delle query SQL usando .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)
