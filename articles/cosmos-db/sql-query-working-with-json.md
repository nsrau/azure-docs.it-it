---
title: Uso di JSON in Azure Cosmos DB
description: Informazioni su come eseguire una query e accedere alle proprietà JSON annidate e usare caratteri speciali in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: a8f32ad69d32844305c1cc785afc9f1df3c102b8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006352"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Uso di JSON in Azure Cosmos DB

Nell'API SQL (Core) di Azure Cosmos DB, gli elementi vengono archiviati come JSON. Il sistema di tipi e le espressioni sono limitati a gestire solo i tipi JSON. Per ulteriori informazioni, vedere la [specifica JSON](https://www.json.org/).

Verranno riepilogati alcuni aspetti importanti dell'uso di JSON:

- Gli oggetti JSON iniziano sempre con `{` una parentesi graffa sinistra e terminano `}` con una parentesi graffa destra
- È possibile [annidare](#nested-properties) le proprietà JSON tra loro
- I valori delle proprietà JSON possono essere matrici
- I nomi delle proprietà JSON sono maiuscole/minuscole
- Il nome della proprietà JSON può essere qualsiasi valore stringa (inclusi spazi o caratteri che non sono lettere)

## <a name="nested-properties"></a>Proprietà annidate

È possibile accedere a JSON annidato usando una funzione di accesso punto. È possibile usare le proprietà JSON annidate nelle query nello stesso modo in cui è possibile usare qualsiasi altra proprietà.

Ecco un documento con JSON annidato:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

In questo caso, le `state`proprietà `country`, e `city` sono tutte annidate all'interno `address` della proprietà.

Nell'esempio seguente vengono proiettate due proprietà `f.address.state` annidate: e `f.address.city`.

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

## <a name="working-with-arrays"></a>Utilizzo delle matrici

Oltre alle proprietà annidate, JSON supporta anche le matrici.

Ecco un documento di esempio con una matrice:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Quando si utilizzano le matrici, è possibile accedere a un elemento specifico all'interno della matrice facendo riferimento alla relativa posizione:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Nella maggior parte dei casi, tuttavia, si utilizzerà una [sottoquery](sql-query-subquery.md) o un [self-join](sql-query-join.md) quando si utilizzano matrici.

Ecco, ad esempio, un documento che mostra il saldo giornaliero del conto bancario del cliente.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Se si desidera eseguire una query che Mostra tutti i clienti che hanno avuto un saldo negativo a un certo punto, è possibile utilizzare [Exists](sql-query-subquery.md#exists-expression) con una sottoquery:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>Parole chiave riservate e caratteri speciali in JSON

È possibile accedere alle proprietà usando l'operatore `[]`di proprietà tra virgolette. Ad esempio, la sintassi di `SELECT c.grade` and `SELECT c["grade"]` sono equivalenti. Questa sintassi è utile per eseguire l'escape di una proprietà che contiene spazi, caratteri speciali o ha lo stesso nome di una parola chiave SQL o di una parola riservata.

Ad esempio, di seguito è riportato un documento con una `order` proprietà denominata e `price($)` una proprietà che contiene caratteri speciali:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se si eseguono query che includono la proprietà `order` o `price($)` la proprietà, verrà visualizzato un errore di sintassi.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Il risultato è:

`
Syntax error, incorrect syntax near 'order'
`

È necessario riscrivere le stesse query come indicato di seguito:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Espressioni JSON

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

Nell'esempio precedente, la `SELECT` clausola deve creare un oggetto JSON e poiché l'esempio non fornisce alcuna chiave, la clausola usa il nome `$1`della variabile dell'argomento implicito. La query seguente restituisce due variabili di argomento `$1` implicite `$2`: e.

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

## <a name="aliasing"></a>Aliasing

È possibile eseguire l'aliasing esplicito dei valori nelle query. Se una query ha due proprietà con lo stesso nome, usare l'aliasing per rinominare una o entrambe le proprietà in modo che siano ambiguità nel risultato previsto.

### <a name="examples"></a>Esempi

La `AS` parola chiave usata per l'aliasing è facoltativa, come illustrato nell'esempio seguente quando si proietta il secondo `NameInfo`valore come:

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing con parole chiave riservate o caratteri speciali

Non è possibile usare l'aliasing per proiettare un valore come nome di proprietà con uno spazio, un carattere speciale o una parola riservata. Se si vuole modificare la proiezione di un valore in, ad esempio, avere un nome di proprietà con uno spazio, è possibile usare un' [espressione JSON](#json-expressions).

Ad esempio:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           { "JSON expression with a special character": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola WHERE](sql-query-where.md)
