---
title: Sottoquery SQL per Azure Cosmos DB
description: Informazioni sulle sottoquery SQL e i casi d'uso comuni in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514800"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Esempi di sottoquery SQL per Azure Cosmos DB

Una sottoquery è una query nidificata in un'altra query. Una sottoquery è detta anche una query interna o istruzione select interna e l'istruzione che include una sottoquery viene chiamato in genere una query esterna.

Esistono due tipi di sottoquery:

* Correlato - una sottoquery correlate è una sottoquery che fa riferimento a valori dalla query esterna. La sottoquery viene valutata una sola volta per ogni riga che viene elaborato dalla query esterna.

* Correlato a non - oggetto Non correlate sottoquery è una sottoquery che è indipendente dalla query esterna e può essere eseguita in un proprio senza basarsi su query esterna.

> [!NOTE]
> Azure Cosmos DB supporta solo le sottoquery correlate.

## <a name="types-of-subqueries"></a>Tipi di sottoquery

Le sottoquery possono essere ulteriormente classificate in base al numero di righe e colonne che vengono restituiti. Esistono tre tipi diversi:
1.  **Tabella**: Restituisce più colonne e più righe
2.  **Multi-Value**: Restituisce più righe e una singola colonna
3.  **Valore scalare**: Restituisce una singola riga e colonna singola

> [!NOTE]
> Azure Cosmos DB supporta le sottoquery scalari e con più valori

Query di Cosmos DB SQL di Azure restituiscono sempre una sola colonna (un valore semplice o un documento complesso). Pertanto, solo sottoquery scalari e con più valori in precedenza sono applicabili in Azure Cosmos DB. Una sottoquery con più valori utilizzabile solo nella clausola FROM di un'espressione relazionale, mentre una sottoquery scalare può essere utilizzata come un'espressione scalare nell'istruzione SELECT o nella clausola WHERE o un'espressione relazionale nella clausola FROM.


## <a name="multi-value-subqueries"></a>Sottoquery con più valori

Le sottoquery con più valori restituiscono un set di documenti e vengono sempre utilizzate all'interno della clausola FROM. Vengono utilizzati per:

* Ottimizzazione delle espressioni di JOIN 
* La valutazione di espressioni costose una sola volta e che fa riferimento più volte

### <a name="optimize-join-expressions"></a>Ottimizzare le espressioni di JOIN

Sottoquery con più valori è possibile ottimizzare le espressioni di JOIN premendo predicati dopo ogni espressione select-molti anziché dopo tutti i cross join nella clausola WHERE.

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

Per questa query, l'indice corrisponderà a qualsiasi documento che contiene un tag con nome 'alimenti per formula', un elemento di elementi con un valore compreso tra 0 e 10 e un elemento di gestione con una quantità maggiore di 1. Tuttavia, l'espressione di JOIN qui eseguirà il prodotto incrociato di tutti gli elementi delle matrici di tag, titolo e servings per ogni documento corrisponda prima di applicare qualsiasi filtro. La clausola WHERE applicherà quindi la tupla di filtro predicato in ogni < c, t, n, s >. Ad esempio, se un documento corrispondente ha 10 elementi in ognuna delle tre matrici, verrà ampliato e 1 x 10 x 10 x 10 (vale a dire 1.000) Tuple. Utilizzo di sottoquery in questo caso, può aiutare a filtrando gli elementi di matrice unito prima di join con la successiva espressione.

Questa query è equivalente a quello precedente ma usa le sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Presupponendo che un solo elemento nella matrice di tag corrisponde al filtro e cinque elementi per titolo e le matrici servings, le espressioni di JOIN si espandono in 1 x 1 x 5 x 5 = 25 elementi invece di 1.000 elementi nella prima query.

### <a name="evaluate-once-and-reference-many-times"></a>Valutare una volta e riferimento più volte

Le sottoquery possono aiutare a ottimizzare le query con espressioni costose, ad esempio funzioni definite dall'utente (UDF) o stringa complessa o espressioni aritmetiche. È possibile utilizzare una sottoquery insieme a un'espressione di JOIN per valutare l'espressione di una volta ma farvi riferimento più volte.

La query seguente esegue due volte GetMaxNutritionValue la funzione definita dall'utente:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Ecco una query equivalente che si esegue solo una volta la funzione definita dall'utente:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Dato il comportamento di prodotto incrociato delle espressioni di JOIN, se è stato possibile valutare l'espressione di funzione definita dall'utente su undefined, è necessario assicurarsi che l'espressione di JOIN sempre produce una singola riga, restituendo un oggetto dalla sottoquery anziché il valore direttamente.
>

Ecco un esempio simile che restituisce un oggetto anziché un valore:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

L'approccio non è limitato a funzioni, definite dall'utente, ma piuttosto su una qualsiasi espressione potenzialmente dispendiosa. Ad esempio, potremmo dedicare lo stesso approccio con la media di funzioni matematiche:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Simulare join con i dati di riferimento esterno

È spesso necessario fare riferimento ai dati statici che vengono modificati raramente, ad esempio le unità di misure o i codici paese. Per tali dati, è preferibile non duplicarla per ogni documento. Come evitare la duplicazione Salva nell'archiviazione e migliorare le prestazioni di scrittura, mantenendo le dimensioni dei documenti più piccoli. Una sottoquery può essere usata qui per simulare la semantica inner join con una raccolta di dati di riferimento.
Ad esempio, prendere in considerazione questo set di dati di riferimento.

| **Unità** | **Nome**            | **Moltiplicatore** | **Unità di base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | Gramma          |
| µg       | Microgram           | 1.00E-06       | Gramma          |
| mg       | Mg           | 1.00E-03       | Gramma          |
| g        | Gramma                | 1.00E + 00       | Gramma          |
| kg       | Kilogram            | 1.00E + 03       | Gramma          |
| Mg       | Megagram            | 1.00E + 06       | Gramma          |
| Gg       | Gigagram            | 1.00E + 09       | Gramma          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E + 00       | Joule         |
| kJ       | Chilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| licenza CAL      | Termodinamica             | 1.00E + 00       | termodinamica       |
| kcal     | Termodinamica             | 1.00E + 03       | termodinamica       |
| IU       | Unità di misura internazionali |                |               |


La query seguente simula un join con i dati in modo che si aggiungere il nome dell'unità per l'output:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Sottoquery scalari

Un'espressione di sottoquery scalare è una sottoquery che restituisce un valore singolo. Il valore dell'espressione di sottoquery scalare è il valore della proiezione della sottoquery (clausola SELECT).  Un'espressione di sottoquery scalare può essere utilizzata in molti punti di un'espressione scalare è valida. Ad esempio, una sottoquery scalare può essere utilizzata in qualsiasi espressione in entrambi l'istruzione SELECT e nelle clausole WHERE.
Tuttavia, utilizzando una sottoquery scalare non sempre consente di ottimizzare. Ad esempio, passando una sottoquery scalare come argomento a un sistema o funzioni definite dall'utente non offre vantaggi nel consumo di UR o latenza.

Sottoquery scalari possono essere ulteriormente classificata come:
* Semplice espressione sottoquery scalari
* Aggregazione sottoquery scalari

### <a name="simple-expression-scalar-subqueries"></a>Semplice espressione sottoquery scalari

Una sottoquery scalare semplice-expression è una sottoquery correlata che ha una clausola SELECT che non contiene eventuali espressioni di aggregazione. Queste sottoquery non offrono alcun vantaggio ottimizzazione perché il compilatore li converte in un'unica espressione semplice più grande. Non è presente alcun contesto correlata tra la query interna ed esterna.

Di seguito sono riportati alcuni esempi:

**Esempio 1**

```sql
SELECT 1 AS a, 2 AS b
```

Questa query potrebbe essere riscritto, usando una sottoquery scalare semplice espressione per:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Entrambe le query genera questo output:

```json
[
  { "a": 1, "b": 2 }
]
```

**Esempio 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Questa query potrebbe essere riscritto, usando una sottoquery scalare semplice espressione per:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Output della query:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Esempio 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Questa query potrebbe essere riscritto, usando una sottoquery scalare semplice espressione per:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Output della query:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

## <a name="aggregate-scalar-subqueries"></a>Aggregazione sottoquery scalari

Una sottoquery scalare aggregazione è una sottoquery che include una funzione di aggregazione nella relativa proiezione o di un filtro che restituisce un valore singolo.

**Esempio 1:**

Ecco una sottoquery con un'espressione singola funzione di aggregazione nella relativa proiezione:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Output della query:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Esempio 2**

Una sottoquery con più espressioni di funzione di aggregazione:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Output della query:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Esempio 3**

Una query con una sottoquery di aggregazione in proiezione e filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Output della query:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Un modo migliore per scrivere questa query è per creare un join in sottoquery e fanno riferimento l'alias in entrambi l'istruzione SELECT sottoquery e nelle clausole WHERE. Questa query è più efficiente perché è necessario eseguire la sottoquery solo all'interno dell'istruzione join e non in entrambi la proiezione e filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Espressione EXISTS

Azure Cosmos DB supporta le espressioni EXISTS. Si tratta di una sottoquery scalare aggregazione incorporata l'API SQL di Azure Cosmos DB. EXISTS è un'espressione booleana che accetta un'espressione di sottoquery e restituisce true se la sottoquery restituisce tutte le righe; in caso contrario, restituisce false.
Poiché l'API SQL di Azure Cosmos DB non distingue tra espressioni booleane ed eventuali altre espressioni scalari, EXISTS può essere usato in entrambi SELECT e nelle clausole WHERE. Si tratta a differenza di T-SQL, in cui un'espressione booleana (ad esempio, EXISTS, BETWEEN e IN) è limitata al filtro.

Se la sottoquery EXISTS restituisce un valore singolo che non è definito, quindi EXISTS restituirà false. Ad esempio, si consideri la query seguente restituisce false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Tuttavia, se si omette la parola chiave VALUE della sottoquery precedente quindi la query restituirà true:
```sql
SELECT EXISTS (SELECT undefined) 
```

La sottoquery racchiuderà l'elenco dei valori nell'elenco di selezione in un oggetto. Se l'elenco selezionato non contiene valori, la sottoquery restituirà il valore singolo '{}' che è definita e pertanto è presente restituisce true.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Esempio: Riscrittura ARRAY_CONTAINS e JOIN Exists

Un caso d'uso comuni di ARRAY_CONTAINS consiste nel filtrare un documento dall'esistenza di un elemento in una matrice. In questo caso, corso un controllo per verificare se la matrice tags contiene un'elemento denominata arancione.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

La stessa query può essere riscritto per utilizzare EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Inoltre, ARRAY_CONTAINS solo è in grado di controllare se un valore è uguale a qualsiasi elemento all'interno di una matrice. Se per le proprietà di matrice sono necessari i filtri più complessi, è necessario un JOIN.

Si consideri la query seguente che i filtri in base alle unità e nutritionValue proprietà nella matrice: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Per ogni documento nella raccolta, viene eseguito un prodotto incrociato con i relativi elementi di matrice. Questa operazione di JOIN rende possibile filtrare le proprietà all'interno della matrice. Tuttavia, il consumo di UR di questa query sarà significativo. Ad esempio, se 1.000 documenti ha 100 elementi in ogni matrice, si espanderà per 1.000 x 100 (ad esempio 100.000) Tuple.

Usando `EXISTS` può aiutare a evitare questo prodotto-incrociato costosa:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In questo caso, abbiamo filtrare elementi di matrice all'interno della sottoquery EXISTS. Se un elemento della matrice corrisponde al filtro, allora abbiamo proiettarlo e `EXISTS` restituisce true.

È possibile anche alias EXISTS e farvi riferimento nella proiezione:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Output della query:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

### <a name="array-expression"></a>Espressione di matrice

Il `ARRAY` espressione può essere utilizzata per proiettare i risultati di una query sotto forma di matrice. Questa espressione può essere utilizzata solo all'interno della clausola SELECT della query.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Output della query:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Come con altre sottoquery, consente di filtrare con i `ARRAY` espressione sono possibili.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Output della query:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Espressioni di matrice possono provenire anche dopo la clausola FROM nelle sottoquery.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Output della query:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi di Query SQL](how-to-sql-query.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dati del documento modello](modeling-data.md)
