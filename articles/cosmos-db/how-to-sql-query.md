---
title: Query SQL per Azure Cosmos DB
description: Informazioni sulla sintassi SQL, sui concetti relativi ai database e sulle query SQL per Cosmos DB. SQL può essere usato come linguaggio di query JSON in Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/15/2018
ms.author: mjbrown
ms.openlocfilehash: fb8e2b11ce6178c10fb9dc7d13c5c7d817ece821
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344109"
---
# <a name="sql-query-examples-to-query-data-from-azure-cosmos-db"></a>Esempi di query SQL per eseguire query sui dati di Azure Cosmos DB

Azure Cosmos DB supporta l'esecuzione di query sugli elementi mediante SQL (Structured Query Language) come linguaggio di query JSON per gli account API SQL. Durante la progettazione del linguaggio di query per Azure Cosmos DB, sono considerati i seguenti due obiettivi:

* Invece di inventare un nuovo linguaggio di query, abbiamo fatto in modo che Azure Cosmos DB supporti SQL, uno dei linguaggi di query più familiari e popolari. Azure Cosmos DB SQL fornisce un modello di programmazione formale per le query complesse sugli elementi JSON.  

* Azure Cosmos DB usa il modello di programmazione di JavaScript come base per il linguaggio di query. L'API SQL è radicata nel sistema di tipi, nella valutazione delle espressioni e nella chiamata di funzioni di JavaScript. Questo rappresenta a sua volta un modello di programmazione naturale per le proiezioni relazionali, la navigazione gerarchica attraverso gli elementi JSON, i self join, le query spaziali e la chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript, tra le altre funzionalità.

Questo articolo illustra alcuni esempi di query SQL usando semplici elementi JSON. Per altre informazioni sulla sintassi del linguaggio SQL di Azure Cosmos DB, vedere l’articolo [Riferimento alla sintassi SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a> Introduzione ai comandi di SQL

Creare due semplici elementi JSON ed eseguire query su tali dati. Prendere in considerazione due elementi JSON come le famiglie, inserire gli elementi JSON in un contenitore e successivamente eseguire una query sui dati. In questo caso è illustrato un semplice elemento JSON relativo alle famiglie Andersen e Wakefield: i genitori, i figli (e i loro animali domestici), l'indirizzo e le informazioni di registrazione. L'elemento contiene stringhe, numeri, valori booleani, matrici e proprietà annidate.

**Elemento 1**

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Ecco un secondo elemento con una sottile differenza: vengono usati `givenName` e `familyName` invece di `firstName` e `lastName`.

**Elemento 2**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

A questo punto è possibile provare a eseguire alcune query a fronte di questi dati per comprendere alcuni aspetti chiave del linguaggio delle query SQL di Azure Cosmos DB.

**Query1**: ad esempio, la query seguente restituisce gli elementi in cui il campo ID corrisponde a `AndersenFamily`. Poiché si tratta di `SELECT *`, l'output della query è l'elemento JSON completo. Per altre informazioni sulla sintassi, vedere [Istruzione SELECT](sql-api-query-reference.md#select-query):

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Query2:** Si prenda ora in considerazione il caso in cui fosse necessario modificare la formattazione dell'output JSON in una forma differente. Questa query proietta un nuovo oggetto JSON con due campi selezionati, Name e City, quando la città in cui si trova l'indirizzo ha lo stesso nome dello stato. In questo caso, "NY, NY" corrispondono.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

**Risultati**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

**Query3**: questa query restituisce i nomi di elementi figlio specificati nella famiglia con ID corrispondente a `WakefieldFamily` ordinato in base alla città di residenza.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Risultati**

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Ecco alcuni aspetti del linguaggio di query di Cosmos DB attraverso gli esempi visti finora:  

* Poiché l'API SQL elabora i valori JSON, deve gestire entità con struttura ad albero invece di righe e colonne. Di conseguenza, il linguaggio consente di fare riferimento ai nodi dell'albero a qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem`, in modo analogo al linguaggio SQL relazionale con il riferimento in due parti di `<table>.<column>`.

* Il linguaggio strutturato di interrogazione funziona con dati senza schema. perciò il sistema di tipi deve essere associato in modo dinamico. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che appartenga a uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Ciò significa che il sistema di tipi e le espressioni sono limitati all'interazione esclusiva con i tipi JSON. Per altri dettagli, vedere le [specifiche JSON](http://www.json.org/).  

* Un contenitore Cosmos DB è una raccolta senza schema degli elementi JSON. Le relazioni nelle entità di dati all'interno e tra gli elementi in un contenitore vengono implicitamente acquisite dal contenitore e non dalle relazioni chiave primaria e chiave esterna. È un aspetto importante da sottolineare alla luce dei join tra elementi, descritti più avanti in questo articolo.

## <a id="SelectClause"></a>Clausola SELECT

Ogni query consiste in una clausola SELECT e clausole FROM e WHERE facoltative in base agli standard ANSI-SQL. In genere, l'origine nella clausola FROM per ogni query viene enumerata, quindi il filtro nella clausola WHERE viene applicato all'origine per recuperare un sottoinsieme di elementi JSON. Infine, viene usata la clausola SELECT per proiettare i valori JSON richiesti nell'elenco selezionato. Per altre informazioni sulla sintassi, vedere [sintassi SELECT](sql-api-query-reference.md#bk_select_query).

L'esempio seguente illustra una tipica query SELECT.

**Query**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Proprietà annidate

Nell'esempio seguente vengono proiettate due proprietà annidate `f.address.state` and `f.address.city`.

**Query**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "state": "WA",
      "city": "seattle"
    }]
```

La proiezione supporta anche le espressioni JSON, come illustrato nell'esempio seguente:

**Query**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle",
        "name": "AndersenFamily"
      }
    }]
```

Verrà ora esaminato il ruolo di `$1` . La clausola `SELECT` deve creare un oggetto JSON e, poiché non è stata fornita alcuna chiave, verranno usati i nomi di variabile di argomento implicita che iniziano per `$1`. Ad esempio, questa query restituisce due variabili di argomento implicite, etichettate `$1` and `$2`.

**Query**

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Clausola FROM

La clausola FROM <from_specification> è facoltativa, a meno che l'origine non sia filtrata o proiettata più avanti nella query. Per altre informazioni sulla sintassi, vedere [sintassi FROM](sql-api-query-reference.md#bk_from_clause). Una query come `SELECT * FROM Families` indica che l'intero contenitore Families è il database di origine in base al quale eseguire l'enumerazione. Invece di usare il nome del contenitore, è possibile usare uno speciale identificatore ROOT per rappresentare il contenitore.
L'elenco seguente include le regole applicate per ogni query:

* È possibile effettuare l'aliasing del contenitore, come in `SELECT f.id FROM Families AS f` o semplicemente in `SELECT f.id FROM Families f`. Qui `f` è l'equivalente di `Families`. `AS` è una parola chiave facoltativa per eseguire l'aliasing dell'identificatore.  

* Una volta eseguito l'aliasing, non sarà più possibile associare l'origine iniziale. Ad esempio, la sintassi di `SELECT Families.id FROM Families f` non è valida perché non è più possibile risolvere l'identificatore "Families".  

* Tutte le proprietà a cui è necessario fare riferimento devono essere complete. In assenza di una rigorosa aderenza allo schema, ciò viene applicato per evitare eventuali associazioni ambigue. Di conseguenza, la sintassi di `SELECT id FROM Families f` non è valida perché la proprietà `id` non è associata.

### <a name="get-subitems-using-from-clause"></a>Ottenere gli elementi secondari usando la clausola FROM

È anche possibile ridurre il database di origine a un sottoinsieme di dimensioni inferiori. Ad esempio, per enumerare un solo sottoalbero in ogni elemento, la sottoradice può quindi diventare l'origine, come nell'esempio seguente:

**Query**

```sql
    SELECT *
    FROM Families.children
```

**Risultati**

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

Se nell'esempio precedente veniva usata una matrice come origine, è possibile usare anche un oggetto come origine, come illustrato nell'esempio seguente: Qualsiasi valore JSON valido (diverso da Undefined) che è disponibile nell'origine è considerato per essere incluso nel risultato della query. Se alcune famiglie non hanno un valore `address.state` vengono escluse dal risultato della query.

**Query**

```sql
    SELECT *
    FROM Families.address.state
```

**Risultati**

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Clausola WHERE

La clausola WHERE (**`WHERE <filter_condition>`**) è facoltativa. Specifica la condizione (o le condizioni) che gli elementi JSON forniti dall'origine devono soddisfare per essere inclusi come parte del risultato. Per essere considerato per il risultato, qualsiasi elemento JSON deve valutare le condizioni specificate come "true". La clausola WHERE viene usata dal livello di indice allo scopo di determinare il sottoinsieme più piccolo in assoluto di elementi di origine che possono fare parte del risultato. Per altre informazioni sulla sintassi, vedere [sintassi WHERE](sql-api-query-reference.md#bk_where_clause).

La query seguente richiede elementi che contengono una proprietà nome il cui valore è `AndersenFamily`. Qualsiasi altro elemento che non contiene una proprietà nome o il cui valore non corrisponde a `AndersenFamily` verrà escluso.

**Query**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. Il linguaggio API SQL supporta anche una varietà di espressioni scalari. Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

Gli operatori binari seguenti sono attualmente supportati e possono essere usati nelle query come illustrato negli esempi riportati di seguito:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|string     |  \|\| (concatenazione) |

Saranno ora prese in esame alcune query che usano gli operatori binari.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Sono supportati anche gli operatori unari +, -, ~ e NOT, che possono essere usati all'interno delle query come illustrato di seguito:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Oltre agli operatori binari e unari, sono consentiti anche i riferimenti di proprietà. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON contenente la proprietà `isRegistered` dove il valore della proprietà è uguale al valore JSON `true`. Qualsiasi altro valore (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>` e così via) comporta l'esclusione dell'elemento di origine dai risultati. 

### <a name="equality-and-comparison-operators"></a>Operatori di confronto e uguaglianza

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

Per gli altri operatori di confronto, ad esempio >, >=, !=, < e <=, si applicano le regole seguenti:

* Confronto tra risultati dei tipi in Undefined.  
* Confronto tra i risultati di due oggetti o due matrici in Undefined.

Se il risultato dell'espressione scalare nel filtro è Undefined, l'elemento corrispondente non verrà incluso nel risultato, perché Undefined non è logicamente uguale a "true".

## <a name="between-keyword"></a>Parola chiave BETWEEN
È possibile usare anche la parola chiave BETWEEN per esprimere query su intervalli di valori come in SQL ANSI. La parola BETWEEN può essere utilizzata con stringhe o numeri.

Ad esempio, questa query restituisce tutti gli elementi della famiglia in cui la classe frequentata dal primo figlio sia compresa tra 1 e 5 (inclusi).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

A differenza di SQL ANSI, è possibile usare la clausola BETWEEN anche nella clausola FROM, come nell'esempio seguente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

La differenza principale tra l'uso di BETWEEN nell'API SQL e ANSI SQL consiste nel fatto che è possibile esprimere le query di intervallo su proprietà di tipo misto. Ad esempio, "grade" può essere un numero (5) in alcuni elementi e stringhe in altri ("grade4"). In questi casi, come in JavaScript, un confronto tra due tipi diversi restituisce un risultato "undefined" e l'elemento viene ignorato.

> [!NOTE]
> Per accelerare l'esecuzione della query, creare un criterio di indicizzazione che usa un tipo di indice di intervallo su qualsiasi proprietà/percorso numerico filtrato nella clausola BETWEEN.

### <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)
Gli operatori logici funzionano con valori booleani. Le tabelle di veridicità logica per questi operatori sono illustrate di seguito.

**Operator OR**

| Oppure | True  | False | Undefined |
| --- | --- | --- | --- |
| True  |True  |True  |True  |
| False |True  |False |Undefined |
| Undefined |True  |Undefined |Undefined |

**Operatore AND**

| AND | True  | False | Undefined |
| --- | --- | --- | --- |
| True  |True  |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**Operatore NOT**

| NOT |  |
| --- | --- |
| True  |False |
| False |True  |
| Undefined |Undefined |

## <a name="in-keyword"></a>Parola chiave IN

La parola chiave IN consente di controllare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, questa query restituisce tutti gli elementi della famiglia dove l'ID è uno di "WakefieldFamily" o "AndersenFamily".

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

In questo esempio restituisce tutti gli elementi in cui lo stato è uno dei valori specificati.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Operatori Ternary (?) e Coalesce (??)

Gli operatori Ternary e Coalesce possono essere usati per compilare espressioni condizionali, analogamente ai linguaggi di programmazione più diffusi come C# e JavaScript. L'operatore Ternary (?) può essere utile quando si costruiscono rapidamente nuove proprietà JSON. Ad esempio, ora è possibile scrivere query per classificare i livelli di istruzione in forma leggibile, ad esempio principiante/intermedio/avanzati, come mostrato di seguito.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

È anche possibile annidare le chiamate all'operatore come nella query seguente.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel
    FROM Families.children[0] c
```

Come con altri operatori di query, se le proprietà cui viene fatto riferimento nell'espressione condizionale non sono presenti in alcun elemento o se i tipi confrontati sono diversi, tali elementi vengono esclusi dai risultati della query.

L'operatore Coalesce (??) può essere usato per verificare se una proprietà è presente (definita) in un elemento. Questo operatore risulta utile per le query su dati semistrutturati o di tipo misto. Ad esempio, questa query restituisce il valore "lastName" se è presente oppure il valore "surname" se non è presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Funzione di accesso della proprietà di delimitazione
È anche possibile accedere alle proprietà mediante l'operatore della proprietà di delimitazione `[]`. Ad esempio, la sintassi di `SELECT c.grade` and `SELECT c["grade"]` sono equivalenti. Questa sintassi risulta utile quando occorre usare i caratteri di escape per una proprietà che contiene spazi, caratteri speciali o condivide lo stesso nome di una parola chiave SQL o una parola riservata.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Aliasing

L'esempio precedente verrà ora esteso con l'aliasing esplicito dei valori. AS è la parola chiave usata per l'aliasing. È facoltativo, come è possibile vedere durante la proiezione del secondo valore come `NameInfo`.

Nel caso in cui una query avesse due proprietà con lo stesso nome, è necessario usare l'aliasing per rinominare una o entrambe le proprietà, in modo da evitare ambiguità nel risultato proiettato.

**Query**

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Espressioni scalari

Oltre ai riferimenti di proprietà, la clausola SELECT supporta anche le espressioni scalari, ad esempio le costanti, le espressioni aritmetiche, le espressioni logiche e così via. Ad esempio, ecco una semplice query "Hello World".

**Query**

```sql
    SELECT "Hello World"
```

**Risultati**

```json
    [{
      "$1": "Hello World"
    }]
```

Di seguito è presentato un esempio più complesso che usa un'espressione scalare.

**Query**

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

**Risultati**

```json
    [{
      "$1": 1.33333
    }]
```

Nell'esempio seguente, il risultato dell'espressione scalare è un valore booleano.

**Query**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

**Risultati**

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

## <a name="object-and-array-creation"></a>Creazione di oggetti e matrici

Un'altra funzione fondamentale dell'API SQL è la creazione di matrici/oggetti. Nell'esempio precedente è stato creato un nuovo oggetto JSON. In modo analogo, è possibile creare matrici, come illustrato negli esempi seguenti:

**Query**

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

**Risultati**

```json
    [
      {
        "CityState": [
          "seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

## <a id="ValueKeyword"></a>Parola chiave VALUE

La parola chiave **VALUE** consente di restituire un valore JSON. Ad esempio, la query mostrata di seguito restituisce l'espressione scalare `"Hello World"` invece di `{$1: "Hello World"}`.

**Query**

```sql
    SELECT VALUE "Hello World"
```

**Risultati**

```json
    [
      "Hello World"
    ]
```

La query seguente restituisce il valore JSON senza l'etichetta `"address"` nei risultati.

**Query**

```sql
    SELECT VALUE f.address
    FROM Families f
```

**Risultati**

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

L'esempio seguente mostra come restituire valori primitivi JSON (il livello foglia dell'albero JSON).

**Query**

```sql
    SELECT VALUE f.address.state
    FROM Families f
```

**Risultati**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>Operatore *
L'operatore speciale (*) è supportato per proiettare l'elemento così com'è. Quando usato, deve essere l'unico campo proiettato. Benché una query come `SELECT * FROM Families f` sia valida, `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` non lo sono.

**Query**

```sql
    SELECT * 
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Risultati**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>Operatore TOP

La parola chiave TOP può essere usata per limitare il numero di valori restituiti da una query. Se si usa TOP in combinazione con la clausola ORDER BY, il set di risultati è limitato al primo numero N di valori ordinati. In caso contrario, restituisce il primo numero N di risultati in un ordine non definito. Come procedura consigliata, in un'istruzione SELECT, usare sempre una clausola ORDER BY con la clausola TOP. La combinazione di queste due clausole è l'unico modo per indicare in modo prevedibile le righe interessate dalla clausola TOP. 

**Query**

```sql
    SELECT TOP 1 *
    FROM Families f
```

**Risultati**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

È possibile usare TOP con un valore costante (come illustrato in precedenza) o con un valore della variabile usando le query con parametri. Per altre informazioni dettagliate, vedere le query con parametri seguenti.

## <a id="Aggregates"></a>Funzioni di aggregazione

È anche possibile eseguire le aggregazioni nella clausola `SELECT`. Le funzioni di aggregazione eseguono un calcolo su un set di valori e restituiscono un singolo valore. Ad esempio, la query seguente restituisce il numero di elementi della famiglia all'interno del contenitore.

**Query**

```sql
    SELECT COUNT(1)
    FROM Families f
```

**Risultati**

```json
    [{
        "$1": 2
    }]
```

È anche possibile restituire il valore scalare della funzione di aggregazione tramite la parola chiave `VALUE`. Ad esempio, la query seguente restituisce il numero di valori come un singolo numero:

**Query**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

**Risultati**

```json
    [ 2 ]
```

È anche possibile eseguire aggregazioni combinate a filtri. Ad esempio, la query seguente restituisce il numero di elementi con un indirizzo nello Stato di Washington.

**Query**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

**Risultati**

```json
    [ 1 ]
```

La tabella seguente mostra l'elenco delle funzioni di aggregazione supportate nell'API SQL. `SUM`e `AVG` vengono eseguite su valori numerici, mentre `COUNT`, `MIN` e `MAX` possono essere eseguite su numeri, stringhe, valori booleani e valori null.

| Uso | DESCRIZIONE |
|-------|-------------|
| COUNT | Restituisce il numero di elementi nell'espressione. |
| SUM   | Restituisce la somma dei valori nell'espressione. |
| MIN   | Restituisce il valore minimo nell'espressione. |
| MAX   | Restituisce il valore massimo nell'espressione. |
| MEDIA   | Restituisce la media dei valori nell'espressione. |

Le aggregazioni possono essere eseguite anche sui risultati di un'iterazione della matrice. Per altre informazioni, vedere [Array Iteration in Queries](#Iteration) (Iterazione della matrice nelle query).

> [!NOTE]
> Tenere presente che quando si usa Esplora dati del portale di Azure, le query di aggregazione possono restituire risultati parzialmente aggregati su una pagina di query. Gli SDK generano un singolo valore cumulativo in tutte le pagine.
>
> Per eseguire query di aggregazione tramite codice, è necessario .NET SDK 1.12.0, .NET Core SDK 1.1.0 o SDK per Java 1.9.5 o versione successiva.
>

## <a id="OrderByClause"></a>Clausola ORDER BY

Come in SQL ANSI, è possibile includere una clausola Order By facoltativa durante l'esecuzione di query. La clausola può includere un argomento ASC/DESC facoltativo per specificare l'ordine in cui i risultati devono essere recuperati.

Ad esempio, la query seguente recupera le famiglie in ordine di nome della città di residenza.

**Query**

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

**Risultati**

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La query seguente recupera le famiglie in ordine di data di creazione, archiviata come numero che rappresenta il periodo, ovvero il tempo trascorso dall'1 gennaio 1970 in secondi.

**Query**

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

**Risultati**

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

## <a id="Advanced"></a>Concetti avanzati relativi ai database e alle query SQL

### <a id="Iteration"></a>Iterazione

Nell'API SQL è stato aggiunto un nuovo costrutto tramite la parola chiave **IN** per fornire supporto all'iterazione nelle matrici JSON. L'origine FROM fornisce supporto per l'iterazione. Esaminare l'esempio seguente:

**Query**

```sql
    SELECT *
    FROM Families.children
```

**Risultati**

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
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

Esaminare ora un'altra query che esegue l'iterazione sui figli nel contenitore. Notare la differenza nella matrice di output. Questo esempio suddivide `children` e converte i risultati in un'unica matrice.  

**Query**

```sql
    SELECT *
    FROM c IN Families.children
```

**Risultati**

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
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
```

Può essere usato per filtrare ulteriormente ciascuna voce individuale della matrice, come illustrato nell'esempio seguente:

**Query**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Risultati**

```json
    [{
      "givenName": "Lisa"
    }]
```

È anche possibile eseguire l'aggregazione sul risultato dell'iterazione della matrice. Ad esempio, la query seguente conta il numero di figli in tutte le famiglie.

**Query**

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

**Risultati**

```json
    [
      {
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Join

In un database relazionale, la necessità creare un join tra tabelle è importante. È il corollario logico della progettazione di schemi normalizzati. Al contrario, l'API SQL gestisce un modello dati denormalizzato di elementi senza schema, che è l'equivalente logico di un "self-join".

La sintassi supportata dal linguaggio è `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. In generale, questa query restituisce un set di tuple **N** (tupla con valori **N**). Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set. In altri termini, questa query restituisce il prodotto incrociato completo dei set che partecipano al join.

Gli esempi seguenti illustrano il funzionamento della clausola JOIN. Nell'esempio seguente, il risultato è vuoto perché il prodotto incrociato di ciascun elemento dall'origine e un set vuoto è a sua volta vuoto.

**Query**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Risultati**

```json
    [{
    }]
```

Nell'esempio seguente il join avviene tra la radice dell'elemento e la sottoradice di `children`. È un prodotto incrociato tra due oggetti JSON. Il fatto che i figli siano una matrice non è effettivo nel JOIN in quanto in questo esempio si ha a che fare con una singola radice che è anche la matrice dei figli. Di conseguenza, il risultato contiene solo due risultati, perché il prodotto incrociato di ogni elemento con la matrice produce esattamente un solo elemento.

**Query**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Risultati**

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

L'esempio seguente illustra un join più convenzionale:

**Query**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

**Risultati**

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Per prima cosa occorre notare che il valore `from_source` della clausola **JOIN** è un iteratore. Pertanto il flusso in questo caso è il seguente:  

* Espandere ciascun elemento figlio **c** nella matrice.
* Applicare un prodotto incrociato con la radice dell'elemento **f** con ogni elemento figlio **c** che è stato convertito nel primo passaggio.
* Infine, proiettare solo la proprietà nome **f** dell'oggetto radice.

Il primo elemento (`AndersenFamily`) contiene solo un elemento figlio, quindi il set di risultati contiene un singolo oggetto corrispondente a questo elemento. Il secondo elemento (`WakefieldFamily`) contiene due elementi figlio. Quindi, il prodotto incrociato genera un oggetto separato per ogni figlio, dando come risultato due oggetti, uno per ogni figlio corrispondente a questo elemento. I campi radice in entrambi gli elementi sono uguali, proprio come ci si aspetterebbe in un prodotto incrociato.

La vera utilità del JOIN consiste nel formare tuple dal prodotto incrociato in una forma che altrimenti sarebbe difficile proiettare. Come illustrato nell'esempio seguente, è possibile filtrare la combinazione di una tupla che consenta all'utente di scegliere una condizione soddisfatta dall'insieme delle tuple.

**Query**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

**Risultati**

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Questo esempio è un'estensione naturale del precedente e illustra l'esecuzione di un doppio join. Il prodotto incrociato può quindi essere visualizzato come lo pseudo-codice seguente:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` ha un figlio che ha un animale domestico. Il prodotto incrociato genera dunque una riga (1\*1\*1) da questa famiglia. Tuttavia, la famiglia WakefieldFamily ha due figli, ma un solo figlio, "Jesse", ha animali domestici. Jesse ha però due animali domestici. il prodotto incrociato genera dunque 1\*1\*2 = 2 righe da questa famiglia.

Nell'esempio successivo c'è un filtro aggiuntivo in `pet`, che esclude tutte le tuple in cui il nome dell'animale non è "Shadow". Notare che è possibile creare tuple da matrici, filtrare in base a uno qualsiasi degli elementi della tupla e proiettare qualsiasi combinazione degli elementi.

**Query**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

**Risultati**

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>Integrazione JavaScript

Azure Cosmos DB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nei contenitori in termini di stored procedure e trigger. Questo metodo consente di supportare quanto segue:

* Possibilità di eseguire query e operazioni CRUD transazionali con prestazioni elevate sugli elementi in un contenitore grazie alla stretta integrazione del runtime JavaScript direttamente nel motore di database.
* Modellazione naturale del flusso di controllo, definizione dell'ambito delle variabili e assegnazione e integrazione di primitivi di gestione delle eccezioni con transazioni di database. Per altri dettagli sul supporto di Azure Cosmos DB per l'integrazione di JavaScript, vedere la documentazione relativa alla programmabilità lato server di JavaScript.

### <a id="UserDefinedFunctions"></a>Funzioni definite dall'utente (UDF)

Oltre ai tipi già specificati in questo articolo, l'API SQL offre il supporto per le funzioni definite dall'utente (UDF). In particolare, le UDF scalari sono supportate laddove gli sviluppatori possono passare zero o molti argomenti e restituire un unico argomento. Verrà quindi eseguito un controllo per verificare che ciascuno di questi argomenti sia un valore JSON legale.  

La sintassi SQL viene estesa per supportare la logica delle applicazioni personalizzata usando le funzioni definite dall'utente. Le UDF possono essere registrate con l'API SQL ed è quindi possibile fare loro riferimento come parte di una query SQL. In effetti, le UDF sono progettate espressamente per essere chiamate dalle query. Come corollario a questa scelta, le UDF non hanno accesso all'oggetto di contesto a cui possono invece accedere altri tipi di Javascript (stored procedure e trigger). Poiché le query vengono eseguite in sola lettura, è possibile eseguirle sulle repliche primarie o secondarie. Di conseguenza, a differenza di altri tipi di JavaScript, le UDF vengono progettate per l'esecuzione sulle repliche secondarie.

Di seguito è riportato un esempio di come è possibile registrare una UDF nel database di Cosmos DB, in maniera specifica in un contenitore di elementi.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Nell'esempio precedente è stata creata una UDF, denominata `REGEX_MATCH`. Accetta due valori stringa JSON `input` and `pattern` e controlla se il primo corrisponde al modello specificato nel secondo mediante la funzione string.match() di JavaScript.

È ora possibile usare questa UDF in una query in una proiezione. Le UDF devono essere qualificate con il prefisso con distinzione tra maiuscole e minuscole "udf." quando chiamate dall'interno delle query.

> [!NOTE]
> Prima del 17/03/2015, Cosmos DB supportava le chiamate UDF senza il prefisso "udf.", come SELECT REGEX_MATCH(). Questo modello di chiamata è stato deprecato.  
>

**Query**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Risultati**

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

È anche possibile usare l'UDF all'interno di un filtro, come mostrato nell'esempio seguente, anch'esso qualificato con il prefisso:

**Query**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Risultati**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In sostanza, le UDF sono espressioni scalari valide che è possibile usare sia nelle proiezioni sia nei filtri.

Per ampliare la potenza delle UDF, verrà ora analizzato un altro esempio che prevede la logica condizionale:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Di seguito è riportato un esempio per l'esercitazione con le UDF.

**Query**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

**Risultati**

```json
     [
      {
        "city": "seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Come mostra l'esempio precedente, le UDF integrano la potenza del linguaggio JavaScript con quella dell'API SQL per fornire un'interfaccia programmabile avanzata con la quale eseguire una logica condizionale e procedurale complessa con l'ausilio delle capacità di runtime JavaScript integrate.

L'API SQL fornisce gli argomenti alle UDF per ogni elemento nel database di origine nella fase corrente (clausola WHERE o clausola SELECT) di elaborazione della UDF. Il risultato verrà incorporato in maniera uniforme nella pipeline di esecuzione generale. Se le proprietà a cui fanno riferimento i parametri della UDF non sono disponibili nel valore JSON, il parametro verrà considerato come Undefined, quindi la chiamata alla UDF verrà interamente ignorata. Analogamente, se il risultato della UDF è Undefined, non verrà incluso nel risultato.

Riepilogando, le UDF sono un ottimo strumento per eseguire una logica di business complessa come parte della query.

### <a name="operator-evaluation"></a>Valutazione degli operatori

Essendo un database JSON, Cosmos DB esegue un confronto con gli operatori JavaScript e la relativa semantica di valutazione. Benché Cosmos DB provi a mantenere la semantica di JavaScript in termini di supporto JSON, la valutazione dell'operazione devia in alcune istanze.

A differenza del tradizionale linguaggio SQL, nell'API SQL spesso i tipi di valori non sono noti fino al loro recupero dal database. Per poter eseguire le query in maniera efficiente, gran parte degli operatori ha rigorosi requisiti di tipi.

L'API SQL non esegue conversioni implicite, a differenza di JavaScript. Ad esempio, una query come `SELECT * FROM Person p WHERE p.Age = 21` corrisponde a elementi che contengono una proprietà Age il cui valore è 21. Qualsiasi altro elemento la cui proprietà Age corrisponde alla stringa "21", o ad altre possibili variazioni come "021", "21.0", "0021", "00021" e così via, non verrà trovato. Questo comportamento è diverso da quanto avviene in JavaScript, che consente di eseguire implicitamente il cast dei valori di stringa al numero (in base all'operatore, ad esempio: ==). Questa scelta è fondamentale per la ricerca di indici corrispondenti nell'API SQL.

## <a name="parameterized-sql-queries"></a>Query SQL con parametri

Cosmos DB supporta le query con parametri espressi con la consueta notazione \@. SQL con parametri fornisce solide capacità di gestione ed escape dell'input utente, evitando l'esposizione accidentale di dati mediante attacchi SQL injection.

Ad esempio, è possibile scrivere una query che accetta come parametri il cognome e lo stato di residenza e quindi eseguirla per diversi valori di cognome e stato di residenza in base all'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Questa richiesta può quindi essere inviata a Cosmos DB come query con parametri JSON, come illustrato di seguito.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

L'argomento può essere impostato su TOP mediante query con parametri, come illustrato di seguito.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

I valori dei parametri possono essere qualsiasi valore JSON valido (stringhe, numeri, valori booleani, valori null, persino matrici o valori JSON annidati). Inoltre, dato che Cosmos DB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.

## <a id="BuiltinFunctions"></a>Funzioni predefinite

Cosmos DB supporta anche una serie di funzioni predefinite per le operazioni comuni, che possono essere usate all'interno di query come le funzioni definite dall'utente (UDF).

| Gruppo di funzioni | Operazioni |
|---------|----------|
| Funzioni matematiche | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funzioni di controllo del tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funzioni stringa | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funzioni di matrice | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funzioni spaziali | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se attualmente si usa una funzione definita dall'utente (UDF) per cui è ora disponibile una funzione predefinita, è consigliabile usare la corrispondente funzione predefinita perché la sua esecuzione sarà più rapida ed efficiente.

### <a name="mathematical-functions"></a>Funzioni matematiche

Le funzioni matematiche eseguono un calcolo basato su valori di input passati come argomenti e restituiscono un valore numerico. Di seguito è riportata una tabella delle funzioni matematiche predefinite supportate.

| Uso | DESCRIZIONE |
|----------|--------|
| [[ABS (num_expr)](#bk_abs) | Restituisce il valore assoluto (positivo) dell'espressione numerica specificata. |
| [CEILING (num_expr)](#bk_ceiling) | Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata. |
| [FLOOR (num_expr)](#bk_floor) | Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata. |
| [EXP (num_expr)](#bk_exp) | Restituisce l'esponente dell'espressione numerica specificata. |
| [LOG (num_expr [,base])](#bk_log) | Restituisce il logaritmo naturale dell'espressione numerica specificata oppure il logaritmo usando la base specificata |
| [LOG10 (num_expr)](#bk_log10) | Restituisce il valore logaritmico in base 10 dell'espressione numerica specificata. |
| [ROUND (num_expr)](#bk_round) | Restituisce un valore numerico, arrotondato al valore integer più vicino. |
| [TRUNC (num_expr)](#bk_trunc) | Restituisce un valore numerico, troncato al valore integer più vicino. |
| [SQRT (num_expr)](#bk_sqrt) | Restituisce la radica quadrata dell'espressione numerica specificata. |
| [SQUARE (num_expr)](#bk_square) | Restituisce il quadrato dell'espressione numerica specificata. |
| [POWER (num_expr, num_expr)](#bk_power) | Restituisce la potenza dell'espressione numerica specificata al valore specificato. |
| [SIGN (num_expr)](#bk_sign) | Restituisce il valore del segno (-1, 0, 1) dell'espressione numerica specificata. |
| [ACOS (num_expr)](#bk_acos) | Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno. |
| [ASIN (num_expr)](#bk_asin) | Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Questa funzione è detta anche arcoseno. |
| [ATAN (num_expr)](#bk_atan) | Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente. |
| [ATN2 (num_expr)](#bk_atn2) | Restituisce l'angolo, espresso in radianti, tra l'asse x positivo e il raggio dall'origine al punto (y, x), dove x e y sono i valori delle due espressioni float specificate. |
| [COS (num_expr)](#bk_cos) | Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata. |
| [COT (num_expr)](#bk_cot) | Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata. |
| [DEGREES (num_expr)](#bk_degrees) | Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti. |
| [PI ()](#bk_pi) | Restituisce il valore costante di pi greco. |
| [RADIANS (num_expr)](#bk_radians) | Restituisce radianti quando viene immessa un'espressione numerica, espresso in gradi. |
| [SIN (num_expr)](#bk_sin) | Restituisce il seno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata. |
| [TAN (num_expr)](#bk_tan) | Restituisce la tangente dell'espressione di input nell'espressione specificata. |

Ad esempio, è ora possibile eseguire query come illustrato nell'esempio seguente:

**Query**

```sql
    SELECT VALUE ABS(-4)
```

**Risultati**

```json
    [4]
```

La differenza principale tra le funzioni di Cosmos DB rispetto ad ANSI SQL è che sono progettate per interagire correttamente con dati senza schema e con schema misto. Se ad esempio si ha un elemento in cui manca la proprietà Size oppure caratterizzato da un valore non numerico, come "unknown", l'elemento viene ignorato invece di restituire un errore.

### <a name="type-checking-functions"></a>Funzioni di controllo del tipo

Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL. Le funzioni di controllo del tipo possono essere usate per determinare rapidamente il tipo di proprietà all'interno degli elementi, quando è variabile o sconosciuto. Di seguito è riportata una tabella di funzioni predefinite di controllo del tipo supportate.

| **Utilizzo** | **Descrizione** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Restituisce un valore booleano che indica se il tipo del valore è una matrice. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Restituisce un valore booleano che indica se il tipo del valore è booleano. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Restituisce un valore booleano che indica se il tipo del valore è Null. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Restituisce un valore booleano che indica se il tipo del valore è un numero. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Restituisce un valore booleano che indica se il tipo del valore è un oggetto JSON. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Restituisce un valore booleano che indica se il tipo del valore è una stringa. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Restituisce un valore booleano che indica se il tipo del valore è stringa, numero, valore booleano o Null. |

Usando queste funzioni è ora possibile eseguire query come illustrato nell'esempio seguente:

**Query**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Risultati**

```json
    [true]
```

### <a name="string-functions"></a>Funzioni stringa

Le funzioni scalari seguenti eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano. Ecco una tabella di funzioni per stringhe:

| Uso | DESCRIZIONE |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Restituisce il numero di caratteri dell'espressione stringa specificata |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Restituisce parte di un'espressione stringa. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno della prima espressione stringa specificata oppure -1 se la stringa non viene trovata. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Restituisce la parte sinistra di una stringa con il numero specificato di caratteri. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Restituisce la parte destra di una stringa con il numero specificato di caratteri. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Restituisce un'espressione stringa dopo la rimozione di tutti gli spazi vuoti finali. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa. |
| [REPLICATE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) | Ripete un valore stringa in un numero di volte specificato. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Restituisce l'inverso di un valore stringa. |

Usando queste funzioni, è ora possibile eseguire query come le seguenti. Ad esempio, è possibile restituire il nome della famiglia in lettere maiuscole come segue:

**Query**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Risultati**

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

In alternativa, è possibile concatenare stringhe come in questo esempio:

**Query**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Le funzioni stringa possono essere usate anche nella clausola WHERE per filtrare i risultati, come nell'esempio seguente:

**Query**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funzioni di matrice

Le funzioni scalari seguenti eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice. La tabella seguente include funzioni di matrice predefinite:

| Uso | DESCRIZIONE |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Restituisce il numero di elementi dell'espressione di matrice specificato. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Restituisce una matrice che rappresenta il risultato della concatenazione di due o più valori della matrice. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Restituisce un valore booleano che indica se la matrice contiene il valore specificato. Può specificare se la corrispondenza è completa o parziale. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Restituisce parte di un'espressione di matrice. |

Le funzioni di matrice possono essere usate per manipolare le matrici in JSON. Ad esempio, la query seguente restituisce tutti gli elementi in cui uno dei genitori è "Robin Wakefield". 

**Query**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

È possibile specificare un frammento parziale per la corrispondenza di elementi all'interno della matrice. La query seguente trova tutti gli elementi padre con `givenName` di `Robin`.

**Query**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

L'esempio seguente usa ARRAY_LENGTH per ottenere il numero di figli per ogni famiglia.

**Query**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Funzioni spaziali

Cosmos DB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali. 

| Uso | DESCRIZIONE |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Restituisce la distanza tra le due espressioni GeoJSON punto, poligono o LineString. |
| T_WITHIN (point_expr, polygon_expr) | Restituisce un'espressione booleana che indica se il primo oggetto GeoJSON (punto, poligono o LineString) è all'interno del secondo oggetto GeoJSON (punto, poligono o LineString). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Restituisce un'espressione booleana che indica se i due oggetti GeoJSON specificati (punto, poligono o LineString) si intersecano. |
| ST_ISVALID | Restituisce un valore booleano che indica se l'espressione GeoJSON punto, poligono o LineString specificata è valida. |
| ST_ISVALIDDETAILED | Restituisce un valore JSON che contiene un valore booleano valore se l'espressione GeoJSON punto, poligono o LineString specificata è valida e, se non valida, anche il motivo come valore stringa. |

Le funzioni spaziali possono essere utilizzate per eseguire query di prossimità rispetto ai dati spaziali. Ad esempio, di seguito è riportata una query che restituisce tutti gli elementi della famiglia entro 30 km della posizione specificata usando la funzione predefinita ST_DISTANCE.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Per altre informazioni sul supporto geospaziale in Cosmos DB, vedere [Uso dei dati geospaziali in Azure Cosmos DB](geospatial.md). Viene eseguito il wrapping di funzioni spaziali e della sintassi SQL per Cosmos DB. Verrà ora esaminato il funzionamento delle query LINQ e verrà illustrato il modo in cui interagiscono con la sintassi esaminata fino ad ora.

## <a id="Linq"></a>Da LINQ all'API SQL

LINQ è un modello di programmazione .NET che esprime il calcolo come query su flussi di oggetti. Cosmos DB fornisce una libreria lato client che si interfaccia con LINQ agevolando una conversione tra oggetti JSON e .NET e un mapping da un sottoinsieme di query LINQ alle query di Cosmos DB.

L'immagine seguente illustra l'architettura di supporto delle query LINQ usando Cosmos DB.  Con il client di Cosmos DB, gli sviluppatori possono creare un oggetto **IQueryable** che comunica una query al provider di query di Cosmos DB, il quale a sua volta traduce la query LINQ in una query di Cosmos DB. Questa viene quindi passata al server di Cosmos DB per recuperare un set di risultati in formato JSON. I risultati restituiti vengono deserializzati in un flusso di oggetti .NET sul lato client.

![Architettura di supporto delle query LINQ usando API SQL - sintassi SQL, linguaggio di query JSON, concetti relativi ai database e query SQL][1]

### <a name="net-and-json-mapping"></a>Mapping .NET e JSON

Il mapping tra oggetti .NET ed elementi JSON avviene naturalmente: ogni campo del membro dati viene mappato a un oggetto JSON, in cui il nome del campo viene mappato alla parte "chiave" dell'oggetto e la parte "valore" viene mappata in modo ricorsivo alla parte del valore dell'oggetto. Si consideri l'esempio seguente: L'oggetto Family creato viene mappato all'elemento JSON, come illustrato di seguito. Viceversa, l'elemento JSON viene mappato nuovamente a un oggetto .NET.

**Classe C#**

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

**JSON**

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```


### <a name="linq-to-sql-translation"></a>Traduzione LINQ in SQL

Il provider di query di Cosmos DB esegue con il massimo impegno un mapping da una query LINQ a una query SQL di Cosmos DB. Nella descrizione seguente si presuppone che il lettore abbia una certa familiarità con LINQ.

In primo luogo, per il sistema di tipi sono supportati tutti i tipi primitivi JSON: tipi numerici, booleani, stringa e null. Sono supportati solo questi tipi JSON. Sono supportate le seguenti espressioni scalari.

* Valori costanti: includono i valori costanti dei tipo di dati primitivi al momento della valutazione della query.
* Espressioni indice della matrice/di proprietà: queste espressioni si riferiscono alla proprietà di un oggetto o di un elemento matrice.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n è una variabile di tipo int
* Espressioni aritmetiche: includono espressioni aritmetiche comuni su valori numerici e booleani. Per un elenco completo, fare riferimento alle specifiche di SQL.
  
     2 * family.children[0].grade;    x + y;
* Espressione di confronto stringhe: includono il confronto di un valore di stringa con un valore di stringa costante.  
  
     mother.familyName == "Smith";    child.givenName == s; //s è una variabile di stringa
* Espressione di creazione oggetto/matrice: restituisce un oggetto di tipo valore composito o tipo anonimo o ancora un matrice di tali oggetti. Questi valori non possono essere annidati.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //un tipo anonimo con due campi              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Elenco di operatori LINQ supportati

Di seguito è riportato un elenco di operatori LINQ supportati nel provider LINQ incluso in SQL .NET SDK.

* **Select**: le proiezioni traslano in SQL SELECT, inclusa la costruzione di oggetti
* **Where**: i filtri traslano in SQL WHERE e supportano la traslazione tra &&, || e ! in operatori SQL.
* **SelectMany**: consente la rimozione di matrici nella clausola SQL JOIN. Può essere usato per concatenare/annidare le espressioni per filtrare in base agli elementi della matrice.
* **OrderBy e OrderByDescending**: trasla in ORDER BY crescente o decrescente
* Operatori di aggregazione **Count**, **Sum**, **Min**, **Max** e **Average** e relativi equivalenti asincroni **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
* **CompareTo**: trasla in confronti di intervallo. In genere usato per le stringhe in quanto non sono confrontabili in .NET
* **Take**: trasla in SQL TOP per limitare i risultati da una query
* **Math Functions**: supporta la traslazione da Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan e Truncate di .NET nelle funzioni predefinite di SQL equivalenti.
* **String Functions**: supporta la traslazione da Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString e ToUpper di .NET nelle funzioni predefinite di SQL equivalenti.
* **Array Functions**: supporta la traslazione da Concat, Contains e Count di .NET nelle funzioni predefinite di SQL equivalenti.
* **Geospatial Extension Functions**: supporta la traslazione dai metodi sthub Distance, Within, IsValid e IsValidDetailed nelle funzioni predefinite di SQL equivalenti.
* **User-Defined Function Extension Function**: supporta la traslazione dal metodo stub UserDefinedFunctionProvider.Invoke alla funzione corrispondente definita dall'utente.
* **Miscellaneous**: supporta la traslazione degli operatori condizionali e di unione. Consente la conversione di Contains in String CONTAINS, ARRAY_CONTAINS o SQL IN in base al contesto.

### <a name="sql-query-operators"></a>Operatori di query SQL

Di seguito sono riportati alcuni esempi che illustrano in che modo gli operatori di query LINQ standard vengono tradotti in query di Cosmos DB.

#### <a name="select-operator"></a>Operatore Select

La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Espressione lambda LINQ**

```csharp
    input.Select(family => family.parents[0].familyName);
```

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**Espressione lambda LINQ**

```csharp
    input.Select(family => family.children[0].grade + c); // c is an int variable
```

**SQL**

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f
```

**Espressione lambda LINQ**

```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
```

**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName,
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>Operatore SelectMany

La sintassi è `input.SelectMany(x => f(x))`, dove `f` è un'espressione scalare che restituisce un tipo di contenitore.

**Espressione lambda LINQ**

```csharp
    input.SelectMany(family => family.children);
```

**SQL**

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Operatore Where

La sintassi è `input.Where(x => f(x))`, dove `f` è un'espressione scalare che restituisce un valore booleano.

**Espressione lambda LINQ**

```csharp
    input.Where(family=> family.parents[0].familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Espressione lambda LINQ**

```csharp
    input.Where(
        family => family.parents[0].familyName == "Smith" &&
        family.children[0].grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Query SQL composte

Gli operatori sopra riportati possono essere composti in modo da formare query più potenti. Poiché Cosmos DB supporta contenitori annidati, la composizione può essere concatenata o annidata.

#### <a name="concatenation"></a>Concatenazione

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata può iniziare con una query `SelectMany` facoltativa, seguita da più operatori `Select` o `Where`.

**Espressione lambda LINQ**

```csharp
    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Espressione lambda LINQ**

```csharp
    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);
```

**SQL**

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```

**Espressione lambda LINQ**

```csharp
    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**Espressione lambda LINQ**

```csharp
    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```

#### <a name="nesting"></a>Annidamento

La sintassi è `input.SelectMany(x=>x.Q())` dove Q è un operatore `Select`, `SelectMany` o `Where`.

In una query annidata, la query più interna viene applicata a ogni elemento del contenitore esterno. Una funzionalità importante è che la query interna può riferirsi ai campi degli elementi nel contenitore esterno come a self-join.

**Espressione lambda LINQ**

```csharp
    input.SelectMany(family=>
        family.parents.Select(p => p.familyName));
```

**SQL**

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**Espressione lambda LINQ**

```csharp
    input.SelectMany(family =>
        family.children.Where(child => child.familyName == "Jeff"));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```

**Espressione lambda LINQ**

```csharp
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Eseguire query SQL

Cosmos DB espone risorse tramite un'API REST che può essere chiamata da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. In Cosmos DB sono anche disponibili librerie di programmazione per diversi linguaggi comuni, come NET, Node.js, JavaScript e Python. L'API REST e le varie librerie supportano tutte l'esecuzione di query tramite SQL. .NET SDK supporta l'esecuzione di query LINQ oltre a SQL.

Negli esempi seguenti viene illustrato come creare una query e inviarla a fronte di un account di database di Cosmos DB.

### <a id="RestAPI"></a>API REST

Cosmos DB offre un modello di programmazione aperto RESTful su HTTP. È possibile effettuare il provisioning degli account di database usando una sottoscrizione di Azure. Il modello di risorse di Cosmos DB è costituito da un set di risorse disponibili in un account di database e indirizzabili singolarmente tramite un URI logico e stabile. Un set di risorse viene definito feed in questo elemento. Un account database è costituito da un set di database, ognuno dei quali include più contenitori, che possono contenere a loro volta elementi, UDF e altri tipi di risorse.

Il modello di interazione di base con queste risorse usa i verbi HTTP GET, PUT, POST e DELETE con la relativa interpretazione standard. Il verbo POST viene usato per creare una nuova risorsa, per eseguire una stored procedure o per inviare una query di Cosmos DB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Gli esempi seguenti illustrano un'azione POST per una query dell'API SQL eseguita su un contenitore contenente i due elementi di esempio esaminati finora. La query ha un semplice filtro sulla proprietà nome JSON. Si noti l'uso delle intestazioni `x-ms-documentdb-isquery` e Content-Type `application/query+json` per indicare che l'operazione è una query.

**Richiesta**
```
    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

**Risultati**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Il secondo esempio mostra una query più complessa che restituisce più risultati dal join.

**Richiesta**
```
    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

**Risultati**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se il numero di risultati di una query supera le dimensioni di una singola pagina, l'API REST restituisce un token di continuazione attraverso l'intestazione di risposta `x-ms-continuation-token` . I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È possibile controllare il numero di risultati per pagina anche attraverso l'intestazione di numero `x-ms-max-item-count` . Se la query specificata include una funzione di aggregazione come `COUNT`, la pagina di query può restituire un valore parzialmente aggregato nella pagina dei risultati. I client devono eseguire un'aggregazione di secondo livello su questi risultati per ottenere i risultati finali, ad esempio sommare i conteggi restituiti nelle singole pagine per ottenere il conteggio totale.

Per gestire i criteri di coerenza dei dati per le query, usare l'intestazione `x-ms-consistency-level` come tutte le richieste dell'API REST. Ai fini della coerenza della sessione, è necessario anche ripetere l'ultima intestazione cookie `x-ms-session-token` nella richiesta di query. I criteri di indicizzazione del contenitore sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con le impostazioni predefinite dei criteri di indicizzazione, per i contenitori l'indice è sempre aggiornato con il contenuto dell'elemento e i risultati della query corrispondono alla coerenza scelta per i dati. Se i criteri di indicizzazione vengono ridotti alla modalità differita, le query possono restituire risultati obsoleti. Per altre informazioni, vedere [Livelli di coerenza di Azure Cosmos DB][consistency-levels].

Se i criteri di indicizzazione configurati per il contenitore non possono supportare la query specificata, il server di Azure Cosmos DB restituisce il codice di errore 400 (Richiesta non valida). Questo messaggio di errore viene restituito per le query di intervallo per ricerche hash (uguaglianza) e per i percorsi esplicitamente esclusi dall'indicizzazione. È possibile specificare l'intestazione `x-ms-documentdb-query-enable-scan` per consentire alla query di eseguire una scansione quando non è disponibile un indice.

È possibile ottenere metriche dettagliate sull'esecuzione di query impostando l'intestazione `x-ms-documentdb-populatequerymetrics` su `True`. Per altre informazioni, vedere [metriche di query SQL per Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK

.NET SDK supporta l'esecuzione di query LINQ ed SQL. Nell'esempio seguente viene illustrato come eseguire la query di filtro introdotta in precedenza in questo elemento.
```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

In questo esempio vengono confrontate due proprietà per l'uguaglianza all'interno di ciascun elemento, usando le proiezioni anonime.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Nell'esempio successivo vengono illustrati i join, espressi tramite la clausola SelectMany di LINQ.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Il client .NET esegue automaticamente l'iterazione attraverso tutte le pagine dei risultati della query nei blocchi foreach, come sopra illustrato. Le opzioni di query presentate nella sezione dell'API REST sono disponibili anche in .NET SDK usando le classi `FeedOptions` and `FeedResponse` nel metodo CreateDocumentQuery. È possibile controllare il numero di pagine usando l'impostazione `MaxItemCount` .

È anche possibile controllare esplicitamente il paging creando `IDocumentQueryable` tramite l'oggetto `IQueryable`, quindi leggendo i valori ` ResponseContinuationToken` e ritrasferendoli come `RequestContinuationToken` in `FeedOptions`. `EnableScanInQuery` per abilitare le analisi quando la query non può essere supportata dai criteri di indicizzazione configurati. Per i contenitori partizionati, è possibile usare `PartitionKey` per eseguire la query in una partizione singola, anche se Azure Cosmos DB può eseguire l'estrazione automatica dal testo della query, e `EnableCrossPartitionQuery` per eseguire query che potrebbe essere necessario ripetere in più partizioni.

Per altri esempi contenenti query, vedere gli [esempi relativi a .NET in Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet).

### <a id="JavaScriptServerSideApi"></a>API lato server JavaScript

Cosmos DB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nei contenitori usando stored procedure e trigger. La logica JavaScript registrata a livello di contenitore può quindi rilasciare operazioni sugli elementi del contenitore specifico. Viene quindi eseguito il wrapping di queste operazioni nelle transazioni ACID Ambient.

L'esempio seguente mostra come usare queryDocuments nell'API del server JavaScript per eseguire query dall'interno di stored procedure e trigger.

```javascript
    function businessLogic(name, author) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="References"></a>Riferimenti

1. [Introduzione ad Azure Cosmos DB][introduction]
2. [Specifica SQL di Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
4. [Livelli di coerenza di Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [https://json.org/](https://json.org/)
7. Specifiche JavaScript [https://www.ecma-international.org/publications/standards/Ecma-262.htm](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [https://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Tecniche di valutazione di query per database di grandi dimensioni [https://dl.acm.org/citation.cfm?id=152611](https://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
