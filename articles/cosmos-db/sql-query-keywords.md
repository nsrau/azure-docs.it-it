---
title: SQL keywords for Azure Cosmos DB
description: Informazioni sulle parole chiave SQL per il database Cosmos di Azure.Learn about SQL keywords for Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498547"
---
# <a name="keywords-in-azure-cosmos-db"></a>Parole chiave in Azure Cosmos DBKeywords in Azure Cosmos DB

Questo articolo descrive in dettaglio le parole chiave che possono essere usate nelle query SQL del database di Azure.This article details keywords which may be used in Azure Cosmos DB SQL queries.

## <a name="between"></a>BETWEEN

È possibile `BETWEEN` utilizzare la parola chiave per esprimere query su intervalli di valori stringa o numerici. Ad esempio, la query seguente restituisce tutti gli elementi in cui il primo grado figlio è 1-5, inclusi.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

È inoltre possibile `BETWEEN` utilizzare `SELECT` la parola chiave nella clausola, come nell'esempio seguente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Nell'API SQL, a differenza di ANSI SQL, è possibile esprimere query di intervallo su proprietà di tipi misti. Ad esempio, `grade` potrebbe essere `5` un numero come `grade4` in alcuni elementi e una stringa come in altri. In questi casi, come in JavaScript, il confronto `Undefined`tra i due tipi diversi determina , in modo che l'elemento venga ignorato.

> [!TIP]
> Per tempi di esecuzione delle query più rapidi, creare un criterio di `BETWEEN` indicizzazione che usa un tipo di indice di intervallo su qualsiasi proprietà numerica o percorso filtrato dalla clausola.

## <a name="distinct"></a>DISTINCT

La `DISTINCT` parola chiave elimina i duplicati nella proiezione della query.

In questo esempio, la query proietta i valori per ogni cognome:In this example, the query projects values for each last name:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

I risultati sono:

```json
[
    "Andersen"
]
```

È inoltre possibile proiettare oggetti univoci. In questo caso, il campo lastName non esiste in uno dei due documenti, pertanto la query restituisce un oggetto vuoto.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

I risultati sono:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT può essere utilizzato anche nella proiezione all'interno di una sottoquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Questa query proietta una matrice che contiene givenName di ogni figlio con duplicati rimossi. Questa matrice viene alias come ChildNames e proiettata nella query esterna.

I risultati sono:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Le query con una funzione di `DISTINCT` sistema di aggregazione e una sottoquery con non sono supportate. Ad esempio, la query seguente non è supportata:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Utilizzare la parola chiave IN per verificare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, la query seguente restituisce `WakefieldFamily` `AndersenFamily`tutti gli elementi della famiglia in cui è `id` o .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Nell'esempio seguente vengono restituiti tutti gli elementi in cui lo stato è uno dei valori specificati:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

L'API SQL fornisce il supporto per [l'iterazione su matrici JSON](sql-query-object-array.md#Iteration), con un nuovo costrutto aggiunto tramite la parola chiave in nell'origine FROM.

Se si include la `IN` chiave di partizione nel filtro, la query verrà automaticamente filtrata solo per le partizioni pertinenti.

## <a name="top"></a>Torna all'inizio

La parola chiave `N` TOP restituisce il primo numero di risultati della query in un ordine indefinito. Come procedura consigliata, utilizzare `ORDER BY` TOP con la `N` clausola per limitare i risultati al primo numero di valori ordinati. La combinazione di queste due clausole è l'unico modo per indicare in modo prevedibile quali righe si attivano TOP.

È possibile utilizzare TOP con un valore costante, come nell'esempio seguente, o con un valore variabile utilizzando query con parametri.

```sql
    SELECT TOP 1 *
    FROM Families f
```

I risultati sono:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Join](sql-query-join.md)
- [Sottoquery:](sql-query-subquery.md)