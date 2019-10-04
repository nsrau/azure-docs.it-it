---
title: Sottoquery SQL per Azure Cosmos DB
description: Informazioni sulle sottoquery SQL e i casi d'uso comuni in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: cea9963f5073834a24ede44306eb89414909fc83
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003486"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Esempi di sottoquery SQL per Azure Cosmos DB

Una sottoquery è una query nidificata all'interno di un'altra query. Una sottoquery è detta anche query interna o selezione interna. L'istruzione che contiene una sottoquery viene in genere chiamata query esterna.

Questo articolo descrive le sottoquery SQL e i casi d'uso comuni in Azure Cosmos DB. Tutte le query di esempio in questo documento possono essere eseguite su un set di dati nutrizionale precaricato nella [Azure Cosmos DB Playground per le query](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Tipi di sottoquery

Esistono due tipi principali di sottoquery:

* **Correlato**: Sottoquery che fa riferimento a valori della query esterna. La sottoquery viene valutata una volta per ogni riga elaborata dalla query esterna.
* **Non correlato**: Sottoquery indipendente dalla query esterna. Può essere eseguito autonomamente senza basarsi sulla query esterna.

> [!NOTE]
> Azure Cosmos DB supporta solo sottoquery correlate.

Le sottoquery possono essere ulteriormente classificate in base al numero di righe e colonne restituite. Sono disponibili tre tipi:
* **Tabella**: Restituisce più righe e più colonne.
* **Multivalore**: Restituisce più righe e una singola colonna.
* **Scalare**: Restituisce una singola riga e una singola colonna.

Le query SQL in Azure Cosmos DB restituiscono sempre una singola colonna (un semplice valore o un documento complesso). In Azure Cosmos DB sono pertanto applicabili solo sottoquery multivalore e scalare. È possibile utilizzare una sottoquery multivalore solo nella clausola FROM come espressione relazionale. È possibile utilizzare una sottoquery scalare come espressione scalare nella clausola SELECT o WHERE oppure come espressione relazionale nella clausola FROM.

## <a name="multi-value-subqueries"></a>Sottoquery multivalore

Le sottoquery multivalore restituiscono un set di documenti e vengono sempre utilizzate all'interno della clausola FROM. Sono usati per:

* Ottimizzazione delle espressioni di JOIN. 
* Valutazione di espressioni onerose una volta e riferimento più volte.

## <a name="optimize-join-expressions"></a>Ottimizza espressioni di JOIN

Le sottoquery multivalore consentono di ottimizzare le espressioni di JOIN eseguendo il push dei predicati dopo ogni espressione SELECT-many invece che dopo tutti i cross join nella clausola WHERE.

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

Per questa query, l'indice corrisponderà a qualsiasi documento con un tag denominato "Infant formula". Si tratta di un elemento nutriente con un valore compreso tra 0 e 10 e un elemento di servizio con una quantità maggiore di 1. L'espressione di JOIN eseguirà il prodotto incrociato di tutti gli elementi di tag, nutrienti e servirà matrici per ogni documento corrispondente prima dell'applicazione di qualsiasi filtro. 

La clausola WHERE applicherà quindi il predicato del filtro a ogni tupla < c, t, n, s >. Se, ad esempio, un documento corrispondente include 10 elementi in ognuna delle tre matrici, si espanderà fino a 1 x 10 x 10 x 10 (ovvero 1.000) Tuple. L'uso di sottoquery consente di filtrare gli elementi della matrice aggiunti prima di unirli all'espressione successiva.

Questa query è equivalente a quella precedente, ma usa sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Si supponga che un solo elemento nella matrice dei tag corrisponda al filtro e che siano disponibili cinque elementi sia per i nutrienti che per la conservazione delle matrici. Le espressioni di JOIN si espanderanno quindi a 1 x 1 x 5 x 5 = 25 elementi, anziché 1.000 elementi nella prima query.

## <a name="evaluate-once-and-reference-many-times"></a>Valuta una sola volta e fai riferimento molte volte

Le sottoquery consentono di ottimizzare le query con espressioni costose, ad esempio funzioni definite dall'utente (UDF), stringhe complesse o espressioni aritmetiche. È possibile utilizzare una sottoquery insieme a un'espressione di JOIN per valutare l'espressione una sola volta, ma farvi riferimento più volte.

La query seguente esegue due volte `GetMaxNutritionValue` la funzione definita dall'utente:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Ecco una query equivalente che esegue la funzione definita dall'utente una sola volta:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenere presente il comportamento del prodotto incrociato delle espressioni JOIN. Se l'espressione UDF può restituire undefined, è necessario assicurarsi che l'espressione di JOIN produca sempre una singola riga restituendo un oggetto dalla sottoquery invece che direttamente il valore.
>

Di seguito è riportato un esempio simile che restituisce un oggetto anziché un valore:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

L'approccio non è limitato alle funzioni definite dall'utente. Si applica a qualsiasi espressione potenzialmente costosa. Ad esempio, è possibile adottare lo stesso approccio con la funzione `avg`matematica:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Riprodurre un join con dati di riferimento esterni

Spesso può essere necessario fare riferimento a dati statici che cambiano raramente, ad esempio unità di misura o codici paese. È preferibile non duplicare tali dati per ogni documento. Evitando questa duplicazione si risparmia sull'archiviazione e si migliorano le prestazioni di scrittura mantenendo le dimensioni del documento più ridotte. È possibile utilizzare una sottoquery per simulare la semantica dei join interni con una raccolta di dati di riferimento.

Si consideri, ad esempio, questo set di dati di riferimento:

| **Unità** | **Nome**            | **Moltiplicatore** | **Unità di base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogrammo            | 1.00 E-09       | Gram          |
| μg       | Microgrammi           | 1.00 E-06       | Gram          |
| mg       | Milligrammo           | 1.00 E-03       | Gram          |
| g        | Gram                | 1.00 E + 00       | Gram          |
| kg       | Chilogrammo            | 1.00 E + 03       | Gram          |
| gruppo       | Megagrammo            | 1.00 E + 06       | Gram          |
| GG       | Gigagram            | 1.00 E + 09       | Gram          |
| nJ       | Nanojoule           | 1.00 E-09       | Joule         |
| µJ       | Microjoule          | 1.00 E-06       | Joule         |
| mJ       | Millijoule          | 1.00 E-03       | Joule         |
| U        | Joule               | 1.00 E + 00       | Joule         |
| kJ       | Kilojoule           | 1.00 E + 03       | Joule         |
| MJ       | Megajoule           | 1.00 E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00 E + 09       | Joule         |
| Cal      | Calorie             | 1.00 E + 00       | calorie       |
| kcal     | Calorie             | 1.00 E + 03       | calorie       |
| IU       | Unità internazionali |                |               |


La query seguente simula l'Unione con questi dati in modo da aggiungere il nome dell'unità all'output:

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

Un'espressione di sottoquery scalare è una sottoquery che restituisce un singolo valore. Il valore dell'espressione di sottoquery scalare è il valore della proiezione (clausola SELECT) della sottoquery.  È possibile utilizzare un'espressione di sottoquery scalare in molte posizioni in cui un'espressione scalare è valida. È possibile, ad esempio, utilizzare una sottoquery scalare in qualsiasi espressione nelle clausole SELECT e WHERE.

Tuttavia, l'utilizzo di una sottoquery scalare non sempre consente l'ottimizzazione. Ad esempio, il passaggio di una sottoquery scalare come argomento a una funzione di sistema o a una funzione definita dall'utente non fornisce alcun vantaggio nell'utilizzo delle unità di risorse (UR) o nella latenza.

Le sottoquery scalari possono essere ulteriormente classificate come:
* Sottoquery scalari con espressioni semplici
* Sottoquery scalari aggregate

## <a name="simple-expression-scalar-subqueries"></a>Sottoquery scalari con espressioni semplici

Una sottoquery scalare a espressione semplice è una sottoquery correlata che include una clausola SELECT che non contiene espressioni di aggregazione. Queste sottoquery non forniscono vantaggi di ottimizzazione perché il compilatore le converte in un'espressione semplice più grande. Non esiste alcun contesto correlato tra le query interne ed esterne.

Di seguito sono riportati alcuni esempi:

**Esempio 1**

```sql
SELECT 1 AS a, 2 AS b
```

È possibile riscrivere la query utilizzando una sottoquery scalare di espressione semplice per:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Entrambe le query producono questo output:

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

È possibile riscrivere la query utilizzando una sottoquery scalare di espressione semplice per:

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

È possibile riscrivere la query utilizzando una sottoquery scalare di espressione semplice per:

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

### <a name="aggregate-scalar-subqueries"></a>Sottoquery scalari aggregate

Una sottoquery scalare aggregata è una sottoquery che dispone di una funzione di aggregazione nella proiezione o nel filtro che restituisce un singolo valore.

**Esempio 1:**

Ecco una sottoquery con una singola espressione di funzione di aggregazione nella proiezione:

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

Ecco una sottoquery con più espressioni di funzione di aggregazione:

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

Ecco una query con una sottoquery di aggregazione sia nella proiezione che nel filtro:

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

Un modo più ottimale per scrivere questa query consiste nell'unirsi alla sottoquery e fare riferimento all'alias della sottoquery nelle clausole SELECT e WHERE. Questa query è più efficiente perché è necessario eseguire la sottoquery solo nell'istruzione di join e non sia nella proiezione che nel filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Espressione EXISTs

Azure Cosmos DB supporta le espressioni EXISTs. Si tratta di una sottoquery scalare aggregata incorporata nell'API SQL Azure Cosmos DB. EXISTs è un'espressione booleana che accetta un'espressione di sottoquery e restituisce true se la sottoquery restituisce qualsiasi riga. In caso contrario, restituisce false.

Poiché l'API SQL Azure Cosmos DB non distingue tra le espressioni booleane e qualsiasi altra espressione scalare, è possibile utilizzare EXISTs nelle clausole SELECT e WHERE. Questo è diverso da T-SQL, in cui un'espressione booleana, ad esempio EXISTs, BETWEEN e IN, è limitata al filtro.

Se la sottoquery EXISTs restituisce un singolo valore non definito, EXISTs restituirà false. Si consideri, ad esempio, la query seguente che restituisce false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se la parola chiave VALUE nella sottoquery precedente viene omessa, la query restituirà true:
```sql
SELECT EXISTS (SELECT undefined) 
```

La sottoquery includerà l'elenco dei valori nell'elenco selezionato in un oggetto. Se l'elenco selezionato non contiene valori, la sottoquery restituirà il singolo valore '{}'. Questo valore è definito, pertanto EXISTs restituisce true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Esempio: Riscrittura di ARRAY_CONTAINS e JOIN come EXISTs

Un caso d'uso comune di ARRAY_CONTAINS consiste nel filtrare un documento in base all'esistenza di un elemento in una matrice. In questo caso, viene verificato se la matrice di tag contiene un elemento denominato "Orange".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

È possibile riscrivere la stessa query per utilizzare EXISTs:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Inoltre, ARRAY_CONTAINS può verificare solo se un valore è uguale a qualsiasi elemento all'interno di una matrice. Se sono necessari filtri più complessi sulle proprietà della matrice, usare JOIN.

Si consideri la query seguente che filtra in base `nutritionValue` alle unità e alle proprietà nella matrice: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Per ogni documento della raccolta, viene eseguito un prodotto incrociato con i relativi elementi di matrice. Questa operazione di JOIN consente di filtrare le proprietà all'interno della matrice. Tuttavia, l'utilizzo delle UR della query sarà significativo. Se, ad esempio, 1.000 documenti contengono 100 elementi in ogni matrice, si espanderà a 1.000 x 100 (ovvero 100.000) Tuple.

L'uso di EXISTs può contribuire a evitare questo costoso prodotto incrociato:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In questo caso, si filtrano gli elementi della matrice all'interno della sottoquery EXISTs. Se un elemento di matrice corrisponde al filtro, viene proiettato e EXISTs restituisce true.

È anche possibile alias EXISTs e farvi riferimento nella proiezione:

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

## <a name="array-expression"></a>Espressione di matrice

È possibile utilizzare l'espressione di matrice per proiettare i risultati di una query sotto forma di matrice. È possibile utilizzare questa espressione solo all'interno della clausola SELECT della query.

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

Come per le altre sottoquery, i filtri con l'espressione di matrice sono possibili.

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

Le espressioni di matrice possono anche essere successive alla clausola FROM nelle sottoquery.

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

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dati del documento modello](modeling-data.md)
