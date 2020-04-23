---
title: Risolvere i problemi di query quando si usa Azure Cosmos DBTroubleshoot query issues when using Azure Cosmos DB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi di query SQL del database di Azure Cosmos.Learn how to identify, diagnose, and troubleshoot Azure Cosmos DB SQL query issues.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870446"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Risolvere i problemi di query quando si usa Azure Cosmos DBTroubleshoot query issues when using Azure Cosmos DB

Questo articolo illustra un approccio consigliato generale per la risoluzione dei problemi relativi alle query in Azure Cosmos DB. Anche se non è consigliabile considerare i passaggi descritti in questo articolo una difesa completa contro potenziali problemi di query, qui sono stati inclusi i suggerimenti sulle prestazioni più comuni. È consigliabile usare questo articolo come punto di partenza per la risoluzione dei problemi relativi alle query lente o costose nell'API di base del database di Azure.You should use this article as a starting place for troubleshooting slow or expensive queries in the Azure Cosmos DB core (SQL) API. È anche possibile usare [i log](cosmosdb-monitor-resource-logs.md) di diagnostica per identificare le query lente o che utilizzano quantità significative di velocità effettiva.

È possibile classificare in modo generale le ottimizzazioni delle query in Azure Cosmos DB:You can broadly categorize query optimizations in Azure Cosmos DB:

- Ottimizzazioni che riducono l'addebito dell'unità di richiesta (RU) della query
- Ottimizzazioni che riducono semplicemente la latenza

Se si riduce l'addebito RU di una query, sarà quasi certamente ridurre la latenza pure.

In questo articolo vengono forniti esempi che è possibile ricreare utilizzando il set di dati [nutrizionale.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="common-sdk-issues"></a>Problemi comuni dell'SDK

- Per ottenere prestazioni ottimali, seguire i [suggerimenti sulle prestazioni](performance-tips.md).
    > [!NOTE]
    > Per migliorare le prestazioni, è consigliabile l'elaborazione host di Windows a 64 bit. SQL SDK include un file ServiceInterop.dll nativo per analizzare e ottimizzare le query in locale. ServiceInterop.dll è supportato solo sulla piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop.dll non è disponibile, verrà effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata.
- È possibile `MaxItemCount` impostare un per le query, ma non è possibile specificare un numero minimo di elementi.
    - Il codice deve gestire qualsiasi dimensione `MaxItemCount`di pagina, da zero a .
    - Il numero di elementi in una pagina `MaxItemCount`sarà sempre inferiore a quello specificato. Tuttavia, `MaxItemCount` è rigorosamente un massimo e ci potrebbero essere meno risultati di questo importo.
- A volte le query possono avere pagine vuote anche quando sono presenti risultati in una pagina futura. Le ragioni di questo potrebbero essere:
    - L'SDK potrebbe eseguire più chiamate di rete.
    - La query potrebbe impiegare molto tempo per recuperare i documenti.
- Tutte le query dispongono di un token di continuazione che consentirà alla query di continuare. Assicurarsi di svuotare completamente la query. Esaminare gli esempi SDK `while` e `FeedIterator.HasMoreResults` usare un ciclo per svuotare l'intera query.

## <a name="get-query-metrics"></a>Ottenere metriche di queryGet query metrics

Quando si ottimizza una query in Azure Cosmos DB, il primo passaggio consiste sempre [nell'ottenere le metriche della query](profile-sql-api-query.md) per la query. Queste metriche sono disponibili anche tramite il portale di Azure:These metrics are also available through the Azure portal:

[![Recupero delle](./media/troubleshoot-query-performance/obtain-query-metrics.png) metriche di queryGetting query metrics](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Dopo aver ottenuto le metriche di query, confrontare il conteggio documenti recuperati con il conteggio dei documenti di output per la query. Utilizzare questo confronto per identificare le sezioni pertinenti da esaminare in questo articolo.

Il conteggio documenti recuperati indica il numero di documenti che la query deve caricare. Conteggio documenti di output indica il numero di documenti necessari per i risultati della query. Se il numero di documenti recuperati è significativamente superiore a quello del conteggio dei documenti di output, vi era almeno una parte della query che non era in grado di utilizzare l'indice e doveva eseguire una scansione.

Fare riferimento alle sezioni seguenti per comprendere le ottimizzazioni delle query rilevanti per lo scenario.

### <a name="querys-ru-charge-is-too-high"></a>L'addebito RU di Query è troppo alto

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Il numero di documenti recuperati è significativamente superiore al numero di documenti di output

- [Includere i percorsi necessari nei criteri di indicizzazione.](#include-necessary-paths-in-the-indexing-policy)

- [Comprendere quali funzioni di sistema utilizzano l'indice.](#understand-which-system-functions-use-the-index)

- [Comprendere quali query di aggregazione utilizzano l'indice.](#understand-which-aggregate-queries-use-the-index)

- [Modificare le query che dispongono sia di un filtro che di una clausola ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Ottimizzare le espressioni JOIN utilizzando una sottoquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Il numero di documenti recuperati è approssimativamente uguale a Conteggio documenti di output

- [Evitare le query tra partizioni.](#avoid-cross-partition-queries)

- [Ottimizzare le query che dispongono di filtri su più proprietà.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Modificare le query che dispongono sia di un filtro che di una clausola ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>L'addebito RU di Query è accettabile, ma la latenza è ancora troppo alta

- [Migliorare la prossimità.](#improve-proximity)

- [Aumentare la velocità effettiva di provisioning.](#increase-provisioned-throughput)

- [Aumentare MaxConcurrency.](#increase-maxconcurrency)

- [Aumentare MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query in cui il numero di documenti recuperati supera il numero di documenti di output

 Il conteggio documenti recuperati indica il numero di documenti che la query deve caricare. Conteggio documenti di output indica il numero di documenti necessari per i risultati della query. Se il numero di documenti recuperati è significativamente superiore a quello del conteggio dei documenti di output, vi era almeno una parte della query che non era in grado di utilizzare l'indice e doveva eseguire una scansione.

Di seguito è riportato un esempio di query di analisi che non è stata interamente servita dall'indice:Here's an example of scan query that wasn't entirely served by the index:

Query:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metriche di query:

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

Il numero di documenti recuperati (60,951) è significativamente superiore a Conteggio documenti di output (7), pertanto questa query doveva eseguire un'analisi. In questo caso, la funzione di sistema [UPPER()](sql-query-upper.md) non utilizza l'indice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Includere i percorsi necessari nei criteri di indicizzazione

I criteri di indicizzazione devono `WHERE` riguardare `ORDER BY` tutte le `JOIN`proprietà incluse nelle clausole, nelle clausole e nella maggior parte delle funzioni di sistema. Il percorso specificato nei criteri di indice deve corrispondere (con distinzione tra maiuscole e minuscole) alla proprietà nei documenti JSON.

Se si esegue una query semplice sul set di dati [nutrizionale,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) si osserva un addebito RU molto più basso quando la proprietà nella `WHERE` clausola viene indicizzata:

#### <a name="original"></a>Originale

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

**Ru carica:** 409.51 RU

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

**Ru carica:** 2.98 RU

È possibile aggiungere proprietà ai criteri di indicizzazione in qualsiasi momento, senza alcun effetto sulla disponibilità o sulle prestazioni di scrittura. Se si aggiunge una nuova proprietà all'indice, le query che utilizzano la proprietà utilizzeranno immediatamente il nuovo indice disponibile. La query utilizzerà il nuovo indice durante la compilazione. Pertanto, i risultati delle query potrebbero essere incoerenti mentre è in corso la ricostruzione dell'indice. Se viene indicizzata una nuova proprietà, le query che utilizzano solo indici esistenti non saranno interessate durante la ricostruzione dell'indice. È possibile tenere traccia dello stato di avanzamento della [trasformazione dell'indice.](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)

### <a name="understand-which-system-functions-use-the-index"></a>Comprendere quali funzioni di sistema utilizzano l'indice

Se un'espressione può essere convertita in un intervallo di valori stringa, è possibile utilizzare l'indice. Altrimenti, non può.

Ecco l'elenco di alcune funzioni stringa comuni che possono usare l'indice:Here's the list of some common string functions that can use the index:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) - str_expr, ma solo se la prima num_expr è 0

Di seguito sono riportate alcune funzioni di sistema comuni che non utilizzano l'indice e devono caricare ogni documento:

| **Funzione di sistema**                     | **Idee per l'ottimizzazione**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Usare Ricerca di Azure per la ricerca full-text.                        |
| SUPERIORE/INFERIORE                             | Anziché utilizzare la funzione di sistema per normalizzare i dati per i confronti, normalizzare l'uso di maiuscole e minuscole al momento dell'inserimento. Una query ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'```come diventa . |
| Funzioni matematiche (non aggregate) | Se è necessario calcolare frequentemente un valore nella query, è consigliabile archiviare il valore come proprietà nel documento JSON. |

------

Altre parti della query potrebbero comunque utilizzare l'indice anche se le funzioni di sistema non lo fanno.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Informazioni sulle query di aggregazione che usano l'indice

Nella maggior parte dei casi, le funzioni di sistema aggregate in Azure Cosmos DB utilizzeranno l'indice. Tuttavia, a seconda dei filtri o delle clausole aggiuntive in una query di aggregazione, potrebbe essere necessario caricare un numero elevato di documenti. In genere, il motore di query applicherà prima l'uguaglianza e i filtri di intervallo. Dopo aver applicato questi filtri, il motore di query può valutare filtri aggiuntivi e ricorrere al caricamento dei documenti rimanenti per calcolare l'aggregazione, se necessario.

Ad esempio, date queste due query di esempio, la query con un filtro di uguaglianza e `CONTAINS` funzione di sistema sarà in genere più efficiente di una query con un `CONTAINS` solo filtro di funzione di sistema. Ciò è dovuto al fatto che il filtro di uguaglianza viene `CONTAINS` applicato per primo e utilizza l'indice prima che i documenti debbano essere caricati per il filtro più costoso.

Interrogazione `CONTAINS` con solo filtro - carica RU più alta:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Query con filtro `CONTAINS` di uguaglianza e filtro - carica RU inferiore:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Di seguito sono riportati altri esempi di query di aggregazione che non utilizzeranno completamente l'indice:Here are additional examples of aggregates queries that will not fully use the index:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Query con funzioni di sistema che non utilizzano l'indice

È necessario fare riferimento alla [pagina della funzione](sql-query-system-functions.md) di sistema pertinente per verificare se utilizza l'indice.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Aggregare query con funzioni definite dall'utente (UDF)Aggregate queries with user-defined functions(UDF's)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Query con GROUP BY

L'addebito `GROUP BY` RU di aumenterà con l'aumentare della cardinalità delle proprietà nella `GROUP BY` clausola. In questo esempio, il motore di query `c.foodGroup = "Sausages and Luncheon Meats"` deve caricare ogni documento che corrisponde al filtro in modo che l'addebito RU sia elevato.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Se si prevede di eseguire spesso le stesse query di aggregazione, potrebbe essere più efficiente creare una vista materializzata in tempo reale con il feed di modifiche di [Azure Cosmos DB](change-feed.md) rispetto all'esecuzione di singole query.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Modificare le query che dispongono sia di un filtro che di una clausola ORDER BY

Anche se le query `ORDER BY` che dispongono di un filtro e di una clausola utilizzeranno in genere un indice di intervallo, saranno più efficienti se possono essere servite da un indice composito. Oltre a modificare i criteri di indicizzazione, è necessario aggiungere `ORDER BY` tutte le proprietà nell'indice composito alla clausola. Questa modifica alla query garantirà che utilizzi l'indice composito.  È possibile osservare l'impatto eseguendo una query sul set di dati [nutrizionali:You can](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) observe the impact by running a query on the nutrition dataset:

#### <a name="original"></a>Originale

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

**Ru carica:** 44.28 RU

#### <a name="optimized"></a>Ottimizzato

Query aggiornata (include `ORDER BY` entrambe le proprietà nella clausola):

```sql
SELECT * FROM c
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

**Ru carica:** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Ottimizzare le espressioni JOIN utilizzando una sottoquery
Le sottoquery a `JOIN` più valori possono ottimizzare le espressioni eseguendo il push dei predicati dopo ogni espressione select-many anziché dopo tutti i cross join nella `WHERE` clausola.

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

**Ru carica:** 167.62 RU

Per questa query, l'indice corrisponderà a qualsiasi documento con un tag con il nome "formula per neonati", nutritionValue maggiore di 0 e importo di pubblicazione maggiore di 1. L'espressione `JOIN` qui eseguirà il cross-product di tutti gli elementi di tag, sostanze nutritive e serve array per ogni documento corrispondente prima di applicare qualsiasi filtro. La `WHERE` clausola applicherà quindi il `<c, t, n, s>` predicato del filtro su ogni tupla.

Ad esempio, se un documento corrispondente ha 10 elementi in ognuna delle tre matrici, si espanderà a 1 x 10 x 10 x 10 (vale a dire, 1.000) tuple. L'uso delle sottoquery può essere utile per filtrare gli elementi della matrice unita in join prima di unirsi all'espressione successiva.

Questa query è equivalente a quella precedente, ma utilizza sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru carica:** 22.17 RU

Si supponga che solo un elemento nella matrice di tag corrisponda al filtro e che siano presenti cinque elementi per entrambi gli array nutrients e servings. Le `JOIN` espressioni si espanderanno a 1 x 1 x 5 x 5 x 25 elementi, a differenza di 1.000 elementi nella prima query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query in cui il conteggio dei documenti recuperati è uguale a Conteggio documenti di output

Se il numero di documenti recuperati è approssimativamente uguale a Conteggio documenti di output, la query non è stata necessaria per eseguire la scansione di molti documenti non necessari. Per molte query, come quelle che utilizzano la parola chiave TOP, conteggio documenti recuperati potrebbero superare il numero di documenti di output di 1. Non c'è bisogno che ti preoccupi di questo.

### <a name="avoid-cross-partition-queries"></a>Evitare query tra partizioniAvoid cross partition queries

Azure Cosmos DB usa [il partizionamento](partitioning-overview.md) per ridimensionare i singoli contenitori con l'aumento delle esigenze di archiviazione dei dati e dell'unità richiesta. Ogni partizione fisica ha un indice separato e indipendente. Se la query include un filtro di uguaglianza che corrisponde alla chiave di partizione del contenitore, è necessario controllare solo l'indice della partizione pertinente. Questa ottimizzazione riduce il numero totale di RU necessarie per la query.

Se si dispone di un numero elevato di RU di cui è stato eseguito il provisioning (più di 30.000) o di una grande quantità di dati archiviati (più di circa 100 GB), è probabile che si disponga di un contenitore sufficientemente grande da visualizzare una riduzione significativa degli addebiti per l'RU delle query.

Ad esempio, se si crea un contenitore con la chiave di partizione foodGroup, le query seguenti dovranno controllare solo una singola partizione fisica:For example, if you create a container with the partition key foodGroup, the following queries will need to check only a single physical partition:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Queste query verrebbero inoltre ottimizzate mediante l'aggiunta della chiave di partizione nella query:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Le query che dispongono di filtri di intervallo sulla chiave di partizione o che non dispongono di filtri sulla chiave di partizione dovranno controllare l'indice di ogni partizione fisica per i risultati:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Ottimizzare le query con filtri su più proprietàOptimize queries that have filters on multiple properties

Anche se le query che dispongono di filtri su più proprietà utilizzeranno in genere un indice di intervallo, saranno più efficienti se possono essere servite da un indice composito. Per piccole quantità di dati, questa ottimizzazione non avrà un impatto significativo. Potrebbe essere utile, tuttavia, per grandi quantità di dati. È possibile ottimizzare al massimo, al massimo un filtro non di uguaglianza per indice composito. Se la query include più filtri di non uguaglianza, selezionarne uno che utilizzerà l'indice composito. Il resto continuerà a utilizzare gli indici di intervallo. Il filtro di non uguaglianza deve essere definito per ultimo nell'indice composito. [Ulteriori informazioni sugli indici compositi](index-policy.md#composite-indexes).

Di seguito sono riportati alcuni esempi di query che possono essere ottimizzate con un indice composito:Here are some examples of queries that could be optimized with a composite index:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Ecco l'indice composito pertinente:

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

## <a name="optimizations-that-reduce-query-latency"></a>Ottimizzazioni che riducono la latenza delle queryOptimizations that reduce query latency

In molti casi, l'addebito RU potrebbe essere accettabile quando la latenza delle query è ancora troppo elevata. Nelle sezioni seguenti viene fornita una panoramica dei suggerimenti per la riduzione della latenza delle query. Se si esegue la stessa query più volte sullo stesso set di dati, avrà lo stesso addebito RU ogni volta. Tuttavia, la latenza delle query può variare tra le esecuzioni delle query.

### <a name="improve-proximity"></a>Migliorare la prossimità

Le query eseguite da un'area diversa rispetto all'account del database Cosmos di Azure avranno una latenza maggiore rispetto a quando sono state eseguite all'interno della stessa area. Ad esempio, se si esegue codice nel computer desktop, è necessario prevedere che la latenza sia di decine o centinaia di millisecondi superiore (o superiore) rispetto a quando la query proviene da una macchina virtuale all'interno della stessa area di Azure di Azure Cosmos DB. È semplice distribuire a livello globale i [dati in Azure Cosmos DB](distribute-data-globally.md) per garantire che i dati possano avvicinare l'app.

### <a name="increase-provisioned-throughput"></a>Aumentare la velocità effettiva di provisioning

In Azure Cosmos DB, la velocità effettiva di cui è stato eseguito il provisioning viene misurata in unità richiesta (RU). Si supponga di disporre di una query che utilizza 5 RU di velocità effettiva. Ad esempio, se si esegue il provisioning di 1.000 RU, sarà possibile eseguire la query 200 volte al secondo. Se si è tentato di eseguire la query quando la velocità effettiva disponibile non è sufficiente, Azure Cosmos DB restituirà un errore HTTP 429. Qualsiasi SDK API Core (SQL) corrente tenterà automaticamente questa query dopo l'attesa per un breve periodo di tempo. Le richieste limitate richiedono più tempo, pertanto l'aumento della velocità effettiva di cui è stato eseguito il provisioning può migliorare la latenza delle query. È possibile osservare il numero totale di richieste limitate nel pannello **Metriche** del portale di Azure.You can observe the total number [of throttled requests](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) on the Metrics blade of the Azure portal.

### <a name="increase-maxconcurrency"></a>Aumentare MaxConcurrency

Le query parallele funzionano eseguendo query su più partizioni in parallelo. Tuttavia, i dati di una singola raccolta partizionata vengono recuperati in modo seriale rispetto alla query. Pertanto, se si imposta MaxConcurrency sul numero di partizioni, si ha le migliori possibilità di ottenere la query più performante, a condizione che tutte le altre condizioni di sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare MaxConcurrency (o MaxDegreesOfParallelism nelle versioni precedenti dell'SDK) su un numero elevato. Il sistema sceglierà il minimo (numero di partizioni, input fornito dall'utente) come massimo grado di parallelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentare MaxBufferedItemCount

Le query sono progettate per eseguire il prelettura dei risultati durante l'elaborazione del batch corrente di risultati da parte del client. La prelettura consente di migliorare la latenza complessiva di una query. L'impostazione di MaxBufferedItemCount limita il numero di risultati di prelettura. Se si imposta questo valore sul numero previsto di risultati restituiti (o un numero superiore), la query può ottenere il massimo vantaggio dalla prelettura. Se si imposta questo valore su -1, il sistema determinerà automaticamente il numero di elementi da memorizzare nel buffer.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni su come misurare le RU per query, ottenere statistiche di esecuzione per ottimizzare le query e altro ancora:See the following articles for information on how to measure RUs per query, get execution statistics to tune your queries, and more:

* [Ottenere metriche di esecuzione di query SQL tramite .NET SDKGet SQL query execution metrics by using .NET SDK](profile-sql-api-query.md)
* [Ottimizzazione delle prestazioni delle query con Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Suggerimenti sulle prestazioni per .NET SDK](performance-tips.md)
