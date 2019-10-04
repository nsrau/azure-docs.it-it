---
title: Parole chiave SQL per Azure Cosmos DB
description: Informazioni sulle parole chiave SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342465"
---
# <a name="keywords-in-azure-cosmos-db"></a>Parole chiave in Azure Cosmos DB
Questo articolo illustra in dettaglio le parole chiave che possono essere utilizzate nelle query SQL di Azure Cosmos DB.

## <a name="between"></a>TRA

Come in SQL ANSI, è possibile usare la parola chiave BETWEEN per esprimere query su intervalli di valori di stringa o numerici. Ad esempio, la query seguente restituisce tutti gli elementi in cui del primo elemento figlio risultato è 1 a 5, inclusivo.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

A differenza di SQL ANSI, è possibile utilizzare anche la clausola BETWEEN nella clausola FROM, come nell'esempio seguente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Nell'API SQL, a differenza di SQL ANSI, è possibile esprimere le query di intervallo su proprietà di tipo misto. Ad esempio, `grade` potrebbe essere un numero analogo `5` alcuni elementi e una stringa, ad esempio `grade4` in altri casi. In questi casi, come in JavaScript, il confronto tra i due tipi diversi comporta `Undefined`, in modo che l'elemento viene ignorato.

> [!TIP]
> I tempi di esecuzione query, creare un criterio di indicizzazione che usa un tipo di indice di intervallo su qualsiasi proprietà numeriche o i percorsi che filtra la clausola BETWEEN.

## <a name="distinct"></a>DISTINCT

La parola chiave DISTINCT elimina i duplicati nella proiezione della query.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

In questo esempio, la query proietta i valori per ogni cognome.

I risultati sono:

```json
[
    "Andersen"
]
```

È inoltre possibile proiettare oggetti univoci. In questo caso, il campo lastName non esiste in uno dei due documenti, in modo che la query restituisce un oggetto vuoto.

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

È anche utilizzabile DISTINCT nella proiezione in una sottoquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Questa query proietta una matrice che contiene givenName ogni elemento figlio con i duplicati rimossi. Questa matrice viene usato l'alias ChildNames e proiettati nelle query esterna.

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
## <a name="in"></a> IN

Usare la parola chiave IN per verificare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, la query seguente restituisce tutti gli elementi della famiglia in cui il `id` viene `WakefieldFamily` o `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

L'esempio seguente restituisce tutti gli elementi in cui lo stato è uno dei valori specificati:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

L'API SQL offre il supporto per [iterazione nelle matrici JSON](sql-query-object-array.md#Iteration), con un nuovo costrutto di aggiunte tramite la parola chiave nell'origine FROM. 

## <a name="top"></a>IN ALTO

La parola chiave TOP restituisce il primo `N` numero dei risultati della query in un ordine non definito. Come procedura consigliata, utilizzare TOP con la clausola ORDER BY per limitare i risultati al primo `N` numero di valori ordinati. La combinazione di questi due clausole è l'unico modo per indicare in modo prevedibile che le righe principali influisce.

È possibile utilizzare TOP con un valore costante, come nell'esempio seguente, o con un valore della variabile usando le query con parametri.

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
- [Sottoquery](sql-query-subquery.md)