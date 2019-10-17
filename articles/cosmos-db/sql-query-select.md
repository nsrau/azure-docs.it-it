---
title: Clausola SELECT in Azure Cosmos DB
description: Informazioni sulla clausola SQL SELECT per Azure Cosmos DB. Usare SQL come linguaggio di query JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326797"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Clausola SELECT in Azure Cosmos DB

Ogni query è costituita da una clausola SELECT e dalle clausole [from](sql-query-from.md) e [where](sql-query-where.md) facoltative per gli standard SQL ANSI. In genere, l'origine nella clausola FROM viene enumerata e la clausola WHERE applica un filtro sull'origine per recuperare un subset di elementi JSON. La clausola SELECT proietta quindi i valori JSON richiesti nell'elenco di selezione.

## <a name="syntax"></a>Sintassi

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argomenti
  
- `<select_specification>`  

  Proprietà o valore da selezionare per il set di risultati.  
  
- `'*'`  

  Specifica che il valore deve essere recuperato senza apportare alcuna modifica. In particolare, se il valore elaborato è un oggetto, tutte le proprietà verranno recuperate.  
  
- `<object_property_list>`  
  
  Specifica l'elenco di proprietà da recuperare. Ogni valore restituito sarà un oggetto con le proprietà specificate.  
  
- `VALUE`  

  Specifica che deve essere recuperato il valore JSON anziché l'oggetto JSON completo. A differenza di `<property_list>` non esegue il wrapping del valore previsto in un oggetto.  
 
- `DISTINCT`
  
  Specifica che i duplicati delle proprietà proiettate devono essere rimossi.  

- `<scalar_expression>`  

  Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  

## <a name="remarks"></a>Osservazioni

La sintassi di `SELECT *` è valida solo se la clausola FROM ha dichiarato esattamente un alias. `SELECT *` offre una proiezione dell'identità, che può essere utile se non è necessaria alcuna proiezione. SELECT * è valida solo se viene specificata la clausola FROM e se viene introdotta solo una singola origine di input.  
  
`SELECT <select_list>` e `SELECT *` sono "zucchero sintattico" e possono essere espressi in alternativa usando semplici istruzioni SELECT, come illustrato di seguito.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   Equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   Equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>esempi

L'esempio di query SELECT seguente restituisce `address` da `Families` il cui `id` corrisponde a `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Funzione di accesso della proprietà di delimitazione
È possibile accedere alle proprietà utilizzando l'operatore di proprietà tra virgolette []. Ad esempio, la sintassi di `SELECT c.grade` and `SELECT c["grade"]` sono equivalenti. Questa sintassi è utile per eseguire l'escape di una proprietà che contiene spazi, caratteri speciali o ha lo stesso nome di una parola chiave SQL o di una parola riservata.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Proprietà annidate

Nell'esempio seguente vengono proiettate due proprietà annidate, `f.address.state` e `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Espressioni JSON

La proiezione supporta anche le espressioni JSON, come illustrato nell'esempio seguente:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Nell'esempio precedente, la clausola SELECT deve creare un oggetto JSON e, poiché l'esempio non fornisce alcuna chiave, la clausola usa il nome della variabile di argomento implicita `$1`. La query seguente restituisce due variabili di argomento implicite: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola WHERE](sql-query-where.md)