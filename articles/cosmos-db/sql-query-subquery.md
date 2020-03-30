---
title: SQL subqueries for Azure Cosmos DB
description: Informazioni sulle sottoquery SQL e sui relativi casi d'uso comuni e i diversi tipi di sottoquery in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870565"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>SQL subquery examples for Azure Cosmos DB

Una sottoquery è una query nidificata all'interno di un'altra query. Una sottoquery viene anche definita query interna o selezione interna. L'istruzione che contiene una sottoquery viene in genere definita query esterna.

Questo articolo descrive le sottoquery SQL e i relativi casi d'uso comuni in Azure Cosmos DB. Tutte le query di esempio in questo documento possono essere eseguite su un set di dati nutrizionale precaricato in [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Tipi di sottoquery

Esistono due tipi principali di sottoquery:

* **Correlatid**: Sottoquery che fa riferimento a valori dalla query esterna. La sottoquery viene valutata una volta per ogni riga etere dalla query esterna.
* **Non correlato:** sottoquery indipendente dalla query esterna. Può essere eseguito da solo senza basarsi sulla query esterna.

> [!NOTE]
> Il database Cosmos di Azure supporta solo sottoquery correlate.

Le sottoquery possono essere ulteriormente classificate in base al numero di righe e colonne restituite. Ci sono tre tipi:
* **Tabella**: Restituisce più righe e più colonne.
* **Multivalore**: Restituisce più righe e una singola colonna.
* **Scalare**: Restituisce una singola riga e una singola colonna.

Le query SQL in Azure Cosmos DB restituiscono sempre una singola colonna (un valore semplice o un documento complesso). Pertanto, solo le sottoquery multivalore e scalari sono applicabili in Azure Cosmos DB. È possibile utilizzare una sottoquery multivalore solo nella clausola FROM come espressione relazionale. È possibile utilizzare una sottoquery scalare come espressione scalare nella clausola SELECT o WHERE oppure come espressione relazionale nella clausola FROM.

## <a name="multi-value-subqueries"></a>Sottoquery multivalore

Le sottoquery multivalore restituiscono un set di documenti e vengono sempre utilizzate all'interno della clausola FROM. Sono utilizzati per:

* Ottimizzazione delle espressioni JOIN. 
* Valutazione di espressioni costose una sola volta e riferimento più volte.

## <a name="optimize-join-expressions"></a>Ottimizzare le espressioni JOINOptimize JOIN expressions

Le sottoquery multivalore possono ottimizzare le espressioni JOIN eseguendo il push di predicati dopo ogni espressione select-many anziché dopo tutti i cross join nella clausola WHERE.

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

Per questa query, l'indice corrisponderà a qualsiasi documento con un tag con il nome "formula per neonati". È un elemento nutritivo con un valore compreso tra 0 e 10 e un elemento da portata con una quantità maggiore di 1. L'espressione JOIN qui eseguirà il cross-product di tutti gli elementi di tag, sostanze nutritive e serve array per ogni documento corrispondente prima di applicare qualsiasi filtro. 

La clausola WHERE applicherà quindi il predicato del filtro a ogni <c, t, n, s> tupla. Ad esempio, se un documento corrispondente ha 10 elementi in ognuna delle tre matrici, si espanderà a 1 x 10 x 10 x 10 (vale a dire, 1.000) tuple. L'utilizzo di sottoquery può essere utile per filtrare gli elementi della matrice unita in join prima di unirsi all'espressione successiva.

Questa query è equivalente a quella precedente, ma utilizza sottoquery:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Si supponga che solo un elemento nella matrice di tag corrisponda al filtro e che siano presenti cinque elementi sia per le matrici di nutrienti che per le matrici di serve. Le espressioni JOIN si espanderanno quindi a 1 x 1 x 5 x 5 x 25 elementi, a differenza di 1.000 elementi nella prima query.

## <a name="evaluate-once-and-reference-many-times"></a>Valutare una volta e fare riferimento molte volte

Le sottoquery consentono di ottimizzare le query con espressioni costose, ad esempio funzioni definite dall'utente, stringhe complesse o espressioni aritmetiche. È possibile utilizzare una sottoquery insieme a un'espressione JOIN per valutare l'espressione una sola volta, ma farvi riferimento più volte.

La query seguente esegue `GetMaxNutritionValue` la funzione definita dall'utente due volte:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Di seguito è riportata una query equivalente che esegue la funzione definita dall'utente una sola volta:Here's an equivalent query that runs the UDF only once:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tieni presente il comportamento tra prodotti delle espressioni JOIN. Se l'espressione UDF può restituire undefined, è necessario assicurarsi che l'espressione JOIN produca sempre una singola riga restituendo un oggetto dalla sottoquery anziché direttamente dal valore.
>

Ecco un esempio simile che restituisce un oggetto anziché un valore:Here's a similar example that returns an object rather than a value:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

L'approccio non è limitato alle funzioni definite dall'utente. Si applica a qualsiasi espressione potenzialmente costosa. Ad esempio, è possibile adottare lo `avg`stesso approccio con la funzione matematica :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Imitazione del join con dati di riferimento esterni

Spesso potrebbe essere necessario fare riferimento a dati statici che cambiano raramente, ad esempio unità di misura o codici paese. È preferibile non duplicare tali dati per ogni documento. Evitare questa duplicazione consente di risparmiare sull'archiviazione e migliorare le prestazioni di scrittura mantenendo le dimensioni del documento più piccole. È possibile utilizzare una sottoquery per simulare la semantica di inner join con una raccolta di dati di riferimento.

Si consideri, ad esempio, questo set di dati di riferimento:For instance, consider this set of reference data:

| **Unità** | **Nome**            | **Moltiplicatore** | **Unità di base** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogrammo            | 1.00E-09       | Grammo          |
| Μg       | Microgrammi           | 1.00E-06       | Grammo          |
| mg       | Milligrammo           | 1.00E-03       | Grammo          |
| g        | Grammo                | 1.00E-00       | Grammo          |
| kg       | Chilogrammo            | 1.00E-03       | Grammo          |
| Mg       | Megagramma            | 1.00E-06       | Grammo          |
| Gg       | Gigagram            | 1.00E-09       | Grammo          |
| Nj       | Nanojoule           | 1.00E-09       | Joule         |
| JJ (J)       | Microjoule          | 1.00E-06       | Joule         |
| Mj       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E-00       | Joule         |
| Kj       | Kilojoule           | 1.00E-03       | Joule         |
| Mj       | Megajoule           | 1.00E-06       | Joule         |
| Gj       | Gigajoule           | 1.00E-09       | Joule         |
| cal      | Calorie             | 1.00E-00       | calorie       |
| Kcal     | Calorie             | 1.00E-03       | calorie       |
| Ui       | Unità internazionali |                |               |


La query seguente consente di unire questi dati in modo da aggiungere il nome dell'unità all'output:

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

Un'espressione di sottoquery scalare è una sottoquery che restituisce un singolo valore. Il valore dell'espressione di sottoquery scalare è il valore della proiezione (clausola SELECT) della sottoquery.  È possibile utilizzare un'espressione di sottoquery scalare in molte posizioni in cui un'espressione scalare è valida. Ad esempio, è possibile utilizzare una sottoquery scalare in qualsiasi espressione nelle clausole SELECT e WHERE.

L'utilizzo di una sottoquery scalare non sempre consente di ottimizzare, tuttavia. Ad esempio, il passaggio di una sottoquery scalare come argomento a un sistema o a funzioni definite dall'utente non offre alcun vantaggio nel consumo o nella latenza delle unità di risorsa (RU).

Le sottoquery scalari possono essere ulteriormente classificate come:Scalar subqueries can be further classified as:
* Sottoquery scalari a espressioni semplici
* Sottoquery scalari aggregate

## <a name="simple-expression-scalar-subqueries"></a>Sottoquery scalari a espressioni semplici

Una sottoquery scalare di espressioni semplici è una sottoquery correlata con una clausola SELECT che non contiene espressioni di aggregazione. Queste sottoquery non forniscono alcun vantaggio di ottimizzazione perché il compilatore le converte in un'unica espressione semplice più grande. Non esiste alcun contesto correlato tra le query interne ed esterne.

Di seguito sono riportati alcuni esempi:

**Esempio 1**

```sql
SELECT 1 AS a, 2 AS b
```

È possibile riscrivere questa query usando una sottoquery scalare a espressione semplice, per:You can rewrite this query, by using a simple-expression scalar subquery, to:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Entrambe le query producono questo output:Both queries produce this output:

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

È possibile riscrivere questa query usando una sottoquery scalare a espressione semplice, per:You can rewrite this query, by using a simple-expression scalar subquery, to:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Output query:

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

È possibile riscrivere questa query usando una sottoquery scalare a espressione semplice, per:You can rewrite this query, by using a simple-expression scalar subquery, to:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Output query:

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

Una sottoquery scalare aggregata è una sottoquery con una funzione di aggregazione nella proiezione o nel filtro che restituisce un singolo valore.

**Esempio 1:**

Ecco una sottoquery con una singola espressione di funzione di aggregazione nella sua proiezione:Here's a subquery with a single aggregate function expression in its projection:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Output query:

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

Di seguito è riportata una sottoquery con più espressioni di funzione di aggregazione:Here's a subquery with multiple aggregate function expressions:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Output query:

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

Di seguito è riportata una query con una sottoquery aggregata sia nella proiezione che nel filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Output query:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Un modo più ottimale per scrivere questa query consiste nel partecipare alla sottoquery e fare riferimento all'alias della sottoquery in entrambe le clausole SELECT e WHERE. Questa query è più efficiente perché è necessario eseguire la sottoquery solo all'interno dell'istruzione join e non nella proiezione e nel filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Espressione EXISTS

Azure Cosmos DB supporta le espressioni EXISTS. Si tratta di una sottoquery scalare aggregata incorporata nell'API SQL del database Cosmos di Azure.This is an aggregate scalar subquery built into the Azure Cosmos DB SQL API. EXISTS è un'espressione booleana che accetta un'espressione di sottoquery e restituisce true se la sottoquery restituisce righe. Negli altri casi, viene restituito false.

Poiché l'API SQL del database di Azure non distingue tra espressioni booleane e altre espressioni scalari, è possibile usare EXISTS nelle clausole SELECT e WHERE. Questo è diverso da T-SQL, dove un'espressione booleana (ad esempio, EXISTS, BETWEEN e IN) è limitata al filtro.

Se la sottoquery EXISTS restituisce un singolo valore non definito, EXISTS restituirà false. Si consideri, ad esempio, la query seguente che restituisce false:For instance, consider the following query that evaluates to false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se la parola chiave VALUE nella sottoquery precedente viene omessa, la query restituirà true:
```sql
SELECT EXISTS (SELECT undefined) 
```

La sottoquery racchiuderà l'elenco di valori nell'elenco selezionato in un oggetto. Se l'elenco selezionato non contiene valori, la{}sottoquery restituirà il singolo valore ' '. Questo valore è definito, pertanto EXISTS restituisce true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Esempio: riscrittura di ARRAY_CONTAINS e JOIN come EXISTS

Un caso d'uso comune di ARRAY_CONTAINS consiste nel filtrare un documento in base all'esistenza di un elemento in una matrice. In questo caso, stiamo controllando se la matrice di tag contiene un elemento denominato "arancione".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

È possibile riscrivere la stessa query per utilizzare EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Inoltre, ARRAY_CONTAINS può verificare solo se un valore è uguale a qualsiasi elemento all'interno di una matrice. Se sono necessari filtri più complessi sulle proprietà dell'array, utilizzare JOIN.

Si consideri la query seguente `nutritionValue` che filtra in base alle unità e alle proprietà nell'array: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Per ognuno dei documenti della raccolta, viene eseguito un cross-product con i relativi elementi della matrice. Questa operazione JOIN consente di filtrare le proprietà all'interno della matrice. Tuttavia, il consumo di RU di questa query sarà significativo. Ad esempio, se 1.000 documenti hanno 100 elementi in ogni matrice, si espanderà a 1.000 x 100 (vale a dire, 100.000) tuple.

L'utilizzo di EXISTS può aiutare a evitare questo costoso cross-product:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In questo caso, si filtrano gli elementi della matrice all'interno della sottoquery EXISTS. Se un elemento della matrice corrisponde al filtro, si proietta e EXISTS restituisce true.

È inoltre possibile aliasre EXISTS e farvi riferimento nella proiezione:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Output query:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Espressione ARRAY

È possibile utilizzare l'espressione ARRAY per proiettare i risultati di una query come matrice. È possibile utilizzare questa espressione solo all'interno della clausola SELECT della query.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Output query:

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

Come con altre sottoquery, sono possibili filtri con l'espressione ARRAY.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Output query:

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

Le espressioni di matrice possono anche essere dopo la clausola FROM nelle sottoquery.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Output query:

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
- [Modellare i dati del documento](modeling-data.md)
