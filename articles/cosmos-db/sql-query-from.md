---
title: Clausola FROM in Azure Cosmos DB
description: Informazioni sulla sintassi SQL e sull'esempio per la clausola FROM per Azure Cosmos DB. In questo articolo vengono inoltre illustrati esempi per definire l'ambito dei risultati e ottenere elementi secondari utilizzando la clausola FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587686"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Clausola FROM in Azure Cosmos DB

La clausola FROM (`FROM <from_specification>`) è facoltativa, a meno che l'origine non venga filtrata o proiettata in un secondo momento nella query. Una query `SELECT * FROM Families` come enumera `Families` l'intero contenitore. È anche possibile usare l'identificatore speciale ROOT per il contenitore anziché usare il nome del contenitore.

La clausola FROM applica le regole seguenti per query:

* È possibile effettuare l'aliasing del contenitore, come in `SELECT f.id FROM Families AS f` o semplicemente in `SELECT f.id FROM Families f`. Ecco `f` l'alias `Families`per . AS è una parola chiave facoltativa [per](sql-query-aliasing.md) alias l'identificatore.  

* Una volta creato un alias, non è possibile associare il nome di origine originale. Ad esempio, `SELECT Families.id FROM Families f` non è sintatticamente valido perché l'identificatore `Families` è stato alias e non può più essere risolto.  

* Tutte le proprietà a cui si fa riferimento devono essere completamente qualificate, per evitare associazioni ambigue in assenza di rigida aderenza allo schema. Ad esempio, `SELECT id FROM Families f` non è sintatticamente valido perché la proprietà `id` non è associata.

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
  
- `<container_expression> '.' property_name`  
  
  Specifica che il documento deve essere `property_name` recuperato accedendo alla proprietà.  
  
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

## <a name="examples"></a>Esempi

### <a name="get-subitems-by-using-the-from-clause"></a>Ottenere elementi secondari utilizzando la clausola FROMGet subitems by using the FROM clause

La clausola FROM può ridurre l'origine a un sottoinsieme più piccolo. Per enumerare solo un sottoalbero in ogni elemento, la sottoradice può diventare l'origine, come illustrato nell'esempio seguente:To enumerate only a subtree in each item, the subroot can become the source, as shown in the following example:

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

La query precedente ha utilizzato una matrice come origine, ma è anche possibile usare un oggetto come origine. La query considera qualsiasi valore JSON valido e definito nell'origine per l'inclusione nel risultato. L'esempio seguente `Families` esclude che non `address.state` hanno un valore.

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
