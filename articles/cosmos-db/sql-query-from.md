---
title: Clausola FROM in Azure Cosmos DB
description: Informazioni sulla clausola SQL FROM per Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327002"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Clausola FROM in Azure Cosmos DB

La clausola FROM (`FROM <from_specification>`) è facoltativa, a meno che l'origine non venga filtrata o proiettata più avanti nella query. Una query come `SELECT * FROM Families` enumera l'intero contenitore `Families`. È anche possibile usare la radice identificatore speciale per il contenitore invece di usare il nome del contenitore.

La clausola FROM applica le regole seguenti per ogni query:

* È possibile effettuare l'aliasing del contenitore, come in `SELECT f.id FROM Families AS f` o semplicemente in `SELECT f.id FROM Families f`. Qui `f` è l'alias per `Families`. COME è una parola chiave facoltativa per l' [alias](sql-query-aliasing.md) dell'identificatore.  

* Una volta eseguito l'aliasing, non è possibile associare il nome di origine originale. Ad esempio, `SELECT Families.id FROM Families f` è sintatticamente non valido perché l'identificatore `Families` è stato associato a un alias e non è più possibile risolverlo.  

* Tutte le proprietà a cui viene fatto riferimento devono essere complete per evitare qualsiasi binding ambiguo in assenza di una rigida aderenza allo schema. Ad esempio, `SELECT id FROM Families f` è sintatticamente non valido perché la proprietà `id` non è associata.

## <a name="syntax"></a>Sintassi
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argomenti
  
- `<from_source>`  
  
  Specifica un'origine dati, con o senza un alias. Se non viene specificato, l'alias verrà dedotto da `<container_expression>` usando le seguenti regole:  
  
  -  Se l'espressione è un nome di contenitore, come alias verrà usato il nome del contenitore.  
  
  -  Se l'espressione è `<container_expression>` seguito da un nome di proprietà, come alias verrà usato il nome della proprietà. Se l'espressione è un nome di contenitore, come alias verrà usato il nome del contenitore.  
  
- AS `input_alias`  
  
  Specifica che l'oggetto `input_alias` è un set di valori restituiti dall'espressione di contenitore sottostante.  
 
- `input_alias` IN  
  
  Specifica che `input_alias` deve rappresentare il set di valori ottenuto eseguendo l'iterazione su tutti gli elementi di ogni matrice restituita dall'espressione di contenitore sottostante. Tutti i valori restituiti dall'espressione di contenitore sottostante che non sono matrici vengono ignorati.  
  
- `<container_expression>`  
  
  Specifica l'espressione di contenitore da usare per recuperare i documenti.  
  
- `ROOT`  
  
  Specifica che il documento deve essere recuperato dal contenitore predefinito attualmente connesso.  
  
- `container_name`  
  
  Specifica che il documento deve essere recuperato dal contenitore specificato. Il nome del contenitore deve corrispondere al nome del contenitore a cui si è attualmente connessi.  
  
- `input_alias`  
  
  Specifica che il documento deve essere recuperato dall'altra origine definita dall'alias indicato.  
  
- `<container_expression> '.' property_`  
  
  Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di contenitore specificata.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Specifica che il documento deve essere recuperato eseguendo l'accesso alla proprietà `property_name` o all'elemento di matrice array_index per tutti i documenti recuperati dall'espressione di contenitore specificata.  
  
## <a name="remarks"></a>Osservazioni
  
Tutti gli alias specificati o dedotti in `<from_source>(` devono essere univoci. La sintassi `<container_expression>.`nome proprietà equivale a `<container_expression>' ['"property_name"']'`. Tuttavia, la seconda sintassi può essere usata se un nome di proprietà contiene un carattere non identificatore.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Gestione proprietà mancanti, elementi di matrice mancanti e valori non definiti
  
Se un'espressione di contenitore accede alle proprietà o agli elementi di matrice e il valore non esiste, tale valore verrà ignorato e non elaborato ulteriormente.  
  
### <a name="container-expression-context-scoping"></a>Definizione dell'ambito per il contesto dell'espressione di contenitore  
  
Un'espressione di contenitore può avere come ambito un contenitore o un documento:  
  
-   Un'espressione ha un ambito contenitore se l'origine dell'espressione di contenitore sottostante è ROOT o `container_name`. Un'espressione di questo tipo rappresenta un set di documenti recuperati direttamente dal contenitore e non dipende dall'elaborazione di altre espressioni di contenitore.  
  
-   Un'espressione ha un ambito documento se l'origine dell'espressione di contenitore sottostante è `input_alias` introdotta in precedenza nella query. Tale espressione rappresenta un set di documenti ottenuti dalla valutazione dell'espressione di contenitore nell'ambito di ogni documento appartenente al set associato al contenitore con alias.  Il set risultante sarà un'unione di set ottenuti dalla valutazione dell'espressione di contenitore per ogni documento del set sottostante. 

## <a name="examples"></a>esempi

### <a name="get-subitems-by-using-the-from-clause"></a>Ottenere gli elementi secondari utilizzando la clausola FROM

La clausola FROM può ridurre l'origine a un subset più piccolo. Per enumerare solo un sottoalbero in ogni elemento, la sottoradice può diventare l'origine, come illustrato nell'esempio seguente:

```sql
    SELECT *
    FROM Families.children
```

I risultati sono:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Nella query precedente è stata usata una matrice come origine, ma è anche possibile usare un oggetto come origine. La query considera qualsiasi valore JSON valido e definito nell'origine per l'inclusione nel risultato. Nell'esempio seguente viene escluso `Families` che non hanno un valore `address.state`.

```sql
    SELECT *
    FROM Families.address.state
```

I risultati sono:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola WHERE](sql-query-where.md)