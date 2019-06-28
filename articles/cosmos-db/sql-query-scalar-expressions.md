---
title: Espressioni scalari nelle query SQL di Azure Cosmos DB
description: Informazioni sulle espressioni scalari sintassi SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342780"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Espressioni scalari nelle query SQL di Azure Cosmos DB

Il [clausola SELECT](sql-query-select.md) supporta espressioni scalari. Un'espressione scalare è una combinazione di simboli e operatori che si può valutare per ottenere un singolo valore. Esempi di espressioni scalari includono: le costanti, riferimenti a proprietà, i riferimenti agli elementi di matrice, riferimenti ad alias o chiamate di funzione. Espressioni scalari possono essere combinate in espressioni complesse usando gli operatori.

## <a name="syntax"></a>Sintassi
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argomenti
  
- `<constant>`  
  
   Rappresenta un valore costante. Per informazioni dettagliate, vedere la sezione [Costanti](sql-query-constants.md).  
  
- `input_alias`  
  
   Rappresenta un valore definito dall'elemento `input_alias` introdotto nella clausola `FROM`.  
  Questo valore è sicuramente diverso da un valore **non definito**: i valori **non definiti** presenti nell'input verranno ignorati.  
  
- `<scalar_expression>.property_name`  
  
   Rappresenta un valore della proprietà di un oggetto. Se la proprietà non esiste o si fa riferimento a un valore, che non è un oggetto, quindi l'espressione viene valutata **indefinito** valore.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Rappresenta un valore della proprietà con nome `property_name` o un elemento di matrice con indice `array_index` di una matrice. Se l'indice della proprietà o matrice non esiste o si fa riferimento all'indice della proprietà o matrice per un valore che non è un oggetto o una matrice, l'espressione restituisce un valore non definito.  
  
- `unary_operator <scalar_expression>`  
  
   Rappresenta un operatore applicato a un singolo valore. Per informazioni dettagliate, vedere la sezione [Operatori](sql-query-operators.md).  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Rappresenta un operatore applicato a due valori. Per informazioni dettagliate, vedere la sezione [Operatori](sql-query-operators.md).  
  
- `<scalar_function_expression>`  
  
   Rappresenta un valore definito da un risultato di una chiamata di funzione.  
  
- `udf_scalar_function`  
  
   Nome della funzione scalare definita dall'utente.  
  
- `builtin_scalar_function`  
  
   Nome della funzione scalare predefinita.  
  
- `<create_object_expression>`  
  
   Rappresenta un valore ottenuto creando un nuovo oggetto con proprietà specificate e i relativi valori.  
  
- `<create_array_expression>`  
  
   Rappresenta un valore ottenuto creando una nuova matrice con valori specificati come elementi  
  
- `parameter_name`  
  
   Rappresenta un valore del nome di parametro specificato. I nomi di parametro devono avere un singolo carattere \@ come primo carattere.  
  
## <a name="remarks"></a>Note
  
  Quando si chiama una funzione scalare incorporata o definita dall'utente, è necessario definire tutti gli argomenti. Se uno degli argomenti non è definito, la funzione non verrà chiamata e il risultato sarà indefinito.  
  
  Quando si crea un oggetto, qualsiasi proprietà a cui viene assegnato un valore non definito verrà ignorata e non inclusa nell'oggetto creato.  
  
  Quando si crea una matrice, qualsiasi valore di elemento a cui viene assegnato un valore **non definito** verrà ignorato e non incluso nell'oggetto creato. Ciò causa la sostituzione dell'elemento da parte dell'elemento definito successivo in modo tale che la matrice creata non abbia indici ignorati.  

## <a name="examples"></a>Esempi

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

I risultati sono:

```json
    [{
      "$1": 1.33333
    }]
```

Nella query seguente, il risultato dell'espressione scalare è un valore booleano:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

I risultati sono:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Sottoquery](sql-query-subquery.md)