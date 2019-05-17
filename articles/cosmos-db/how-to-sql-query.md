---
title: Query SQL per Azure Cosmos DB
description: Informazioni sulla sintassi SQL, sui concetti relativi ai database e sulle query SQL per Cosmos DB. Usa SQL come un linguaggio di query JSON di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 4d1ef650a3f12d8b97cbad3e9aecf31c8b81a038
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796154"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Esempi di query SQL per Azure Cosmos DB

Gli account Azure Cosmos DB SQL API supportano l'esecuzione di query di elementi usando Structured Query Language (SQL) come linguaggio di query JSON. Gli obiettivi di progettazione del linguaggio di query di Azure Cosmos DB sono:

* Supporta SQL, uno dei linguaggi query più familiari e popolari, invece di inventare un nuovo linguaggio di query. SQL fornisce un modello di programmazione formale per le query complesse sugli elementi JSON.  

* Per il linguaggio di query, usare il modello di programmazione di JavaScript come base. Sistema di tipi di JavaScript, valutazione delle espressioni e chiamata di funzione sono le radici dell'API SQL. Queste fonti forniscono un modello di programmazione naturale per funzionalità come le proiezioni relazionali, navigazione gerarchica tra gli elementi JSON, i self join, query spaziali e la chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript.

Questo articolo illustra alcuni esempi di query SQL sugli elementi JSON semplici. Per altre informazioni sulla sintassi del linguaggio SQL di Azure Cosmos DB, vedere [riferimento alla sintassi SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Introduzione alle query SQL

Nell'account di API SQL di Cosmos DB, creare un contenitore denominato `Families`. Creare due elementi JSON semplici nel contenitore ed eseguire alcune semplici query su di essi.

### <a name="create-json-items"></a>Creare elementi di JSON

Il codice seguente crea due elementi JSON semplici sulle famiglie. Gli elementi JSON semplici per le famiglie Andersen e Wakefield includono padri, figli e animali domestici, indirizzo e informazioni di registrazione. Il primo elemento ha stringhe, numeri, valori booleani, matrici e proprietà annidate.


```json
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
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Il secondo elemento Usa `givenName` e `familyName` invece di `firstName` e `lastName`.

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
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>La query di elementi JSON

Provare alcune query sui dati JSON per comprendere alcuni aspetti chiave del linguaggio di query SQL di Azure Cosmos DB.

La query seguente restituisce gli elementi in cui il `id` campo corrispondenze `AndersenFamily`. Poiché si tratta un `SELECT *` query, l'output della query è l'elemento JSON completo. Per altre informazioni sulla sintassi SELECT, vedere [istruzione SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati della query sono: 

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

La query seguente riformatta l'output JSON in una forma diversa. La query proietta un nuovo file JSON `Family` oggetto con due campi selezionati `Name` e `City`, quando la città di indirizzo è quello utilizzato per lo stato. In questo caso corrisponde a "NY, NY".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

I risultati della query sono:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

La query seguente restituisce tutti i nomi specificati dei figli della famiglia di prodotti il cui `id` corrisponde a `WakefieldFamily`, ordinato per città.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

I risultati sono:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Gli esempi precedenti illustrano vari aspetti del linguaggio di query di Cosmos DB:  

* Poiché l'API SQL elabora i valori JSON, deve gestire le entità a forma di struttura ad albero invece di righe e colonne. È possibile fare riferimento ai nodi dell'albero a qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem`, in modo analogo al riferimento di due parti del `<table>.<column>` in SQL ANSI.

* Poiché il linguaggio di query funziona con dati senza schema, il sistema di tipi deve essere associato in modo dinamico. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che appartenga a uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Il sistema di tipi e le espressioni sono limitate a trattare unicamente tipi JSON. Per altre informazioni, vedere la [specifica JSON](https://www.json.org/).  

* Un contenitore Cosmos DB è una raccolta senza schema degli elementi JSON. Le relazioni tra gli elementi di contenitore e all'interno vengono implicitamente acquisite dal contenimento, non dalla chiave primaria e le relazioni di chiave esterne. Questa funzionalità è importante per i join intra-elemento descritti più avanti in questo articolo.

## <a id="SelectClause"></a>Clausola SELECT

Ogni query è costituita da una clausola SELECT e FROM facoltativa e le clausole WHERE, come base agli standard ANSI SQL. In genere, l'origine nella clausola FROM viene enumerata e la clausola WHERE viene applicato un filtro sull'origine per recuperare un subset degli elementi JSON. La clausola SELECT proietta quindi i valori JSON richiesti nell'elenco di selezione. Per altre informazioni sulla sintassi, vedere [istruzione SELECT](sql-api-query-reference.md#select-query).

L'istruzione SELECT seguente query restituisce riportato `address` dal `Families` cui `id` corrisponde a `AndersenFamily`:

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

## <a id="EscapingReservedKeywords"></a>Funzione di accesso della proprietà di delimitazione
È possibile accedere alle proprietà usando la proprietà di delimitazione operator []. Ad esempio, la sintassi di `SELECT c.grade` and `SELECT c["grade"]` sono equivalenti. Questa sintassi è utile per eseguire l'escape di una proprietà che contiene spazi, caratteri speciali, che abbia lo stesso nome di una parola chiave SQL o una parola riservata.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Proprietà annidate

L'esempio seguente vengono proiettate due proprietà annidate, `f.address.state` e `f.address.city`.

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

Nell'esempio precedente, è necessario creare un oggetto JSON della clausola SELECT, e poiché il codice di esempio non fornita alcuna chiave, la clausola utilizza il nome di variabile argomento implicito `$1`. La query seguente restituisce due variabili di argomento implicite: `$1` e `$2`.

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

## <a id="ValueKeyword"></a>Parola chiave VALUE

La parola chiave VALUE fornisce un modo per restituire solo il valore JSON. Ad esempio, la query seguente restituisce l'espressione scalare `"Hello World"` invece di `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

La query seguente restituisce i valori JSON senza il `address` etichetta:

```sql
    SELECT VALUE f.address
    FROM Families f
```

I risultati sono:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Nell'esempio seguente viene illustrato come restituire valori primitivi JSON (il livello foglia dell'albero JSON):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

I risultati sono:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>Parola chiave DISTINCT

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

## <a name="aliasing"></a>Aliasing

È possibile in modo esplicito alias valori nelle query. Se una query avesse due proprietà con lo stesso nome, utilizzare l'aliasing per rinominare una o entrambe le proprietà in modo che si sta evitare ambiguità nel risultato proiettato.

La parola chiave utilizzata per l'alias è facoltativa, come illustrato nell'esempio seguente durante la proiezione del secondo valore come `NameInfo`:

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

## <a id="FromClause"></a>Clausola FROM

FROM (`FROM <from_specification>`) clausola è facoltativa, a meno che non sia filtrata o proiettata più avanti nella query di origine. Per altre informazioni sulla sintassi, vedere [dalla sintassi](sql-api-query-reference.md#bk_from_clause). Una query come `SELECT * FROM Families` enumera l'intera `Families` contenitore. È anche possibile usare la speciale identificatore ROOT per il contenitore invece di usare il nome del contenitore.

La clausola FROM vengono applicate le regole seguenti per ogni query:

* È possibile effettuare l'aliasing del contenitore, come in `SELECT f.id FROM Families AS f` o semplicemente in `SELECT f.id FROM Families f`. Di seguito `f` corrisponde all'alias `Families`. COME è l'identificatore di una parola chiave facoltativa per alias.  

* Una volta effettuato l'aliasing, non è possibile associare il nome di origine originale. Ad esempio, `SELECT Families.id FROM Families f` sintatticamente non valido perché l'identificatore `Families` è stato utilizzato un alias e non è più possibile risolvere.  

* Tutte le proprietà di cui viene fatto riferimento devono essere completo evitare eventuali associazioni ambigue in assenza di aderenza allo schema rigoroso. Ad esempio, `SELECT id FROM Families f` sintatticamente non valido perché la proprietà `id` non è associato.

### <a name="get-subitems-by-using-the-from-clause"></a>Ottenere gli elementi secondari usando la clausola FROM

La clausola FROM può ridurre l'origine a un sottoinsieme più piccolo. Per enumerare un solo sottoalbero in ogni elemento, la sottoradice può diventare l'origine, come illustrato nell'esempio seguente:

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

La query precedente utilizzata una matrice come origine, ma è anche possibile usare un oggetto come origine. La query prende in considerazione qualsiasi valore JSON valido, definita nell'origine per l'inclusione nel risultato. L'esempio seguente verrebbe escluderà `Families` che non hanno un `address.state` valore.

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

## <a id="WhereClause"></a>Clausola WHERE

La clausola WHERE facoltativa (`WHERE <filter_condition>`) specifica le condizioni che gli elementi JSON di origine devono soddisfare per la query per includerle nei risultati. Un elemento JSON deve valutare le condizioni specificate come `true` da considerare per il risultato. Il livello di indice viene utilizzata la clausola WHERE per determinare il subset più piccolo di elementi di origine che possono far parte del risultato. Per altre informazioni sulla sintassi, vedere [sintassi WHERE](sql-api-query-reference.md#bk_where_clause).

La query seguente gli elementi di richieste che contengono un' `id` proprietà il cui valore `AndersenFamily`. Esclude qualsiasi elemento che non è un `id` proprietà o il cui valore non corrisponde al `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Espressioni scalari nella clausola WHERE

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. L'API SQL supporta anche vari [espressioni scalari](#scalar-expressions). Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

È possibile usare gli operatori binari supportati seguenti:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (concatenazione) |

Le seguenti query di usano gli operatori binari:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

È anche possibile usare gli operatori unari +,-, ~ e non nelle query, come illustrato negli esempi seguenti:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

È anche possibile usare riferimenti di proprietà nelle query. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON che contiene la proprietà `isRegistered` con valore uguale a `true`. Qualsiasi altro valore, come `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, o `<array>`, esclude l'elemento dal risultato. 

### <a name="equality-and-comparison-operators"></a>Operatori di confronto e uguaglianza

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita |
| **Null** | Non definita | **Ok** | Non definita | Non definita | Non definita | Non definita | Non definita |
| **Boolean** | Non definita | Non definita | **Ok** | Non definita | Non definita | Non definita | Non definita |
| **Number** | Non definita | Non definita | Non definita | **Ok** | Non definita | Non definita | Non definita |
| **String** | Non definita | Non definita | Non definita | Non definita | **Ok** | Non definita | Non definita |
| **Object** | Non definita | Non definita | Non definita | Non definita | Non definita | **Ok** | Non definita |
| **Array** | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita | **Ok** |

Per gli operatori di confronto, ad esempio `>`, `>=`, `!=`, `<`, e `<=`, il confronto tra i tipi o tra due oggetti o matrici produce `Undefined`.  

Se il risultato dell'espressione scalare `Undefined`, l'elemento non è incluso nel risultato, in quanto `Undefined` non è uguale `true`.

### <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti illustrano le tabelle di veridicità logica per questi operatori:

**Operator OR**

| Oppure | True  | Falso | Non definita |
| --- | --- | --- | --- |
| True  |True  |True  |True  |
| Falso |True  |Falso |Non definita |
| Non definita |True  |Non definita |Non definita |

**Operatore AND**

| AND | True  | Falso | Non definita |
| --- | --- | --- | --- |
| True  |True  |Falso |Non definita |
| Falso |Falso |Falso |Falso |
| Non definita |Non definita |Falso |Non definita |

**Operatore NOT**

| NOT |  |
| --- | --- |
| True  |Falso |
| Falso |True  |
| Non definita |Non definita |

## <a name="between-keyword"></a>Parola chiave BETWEEN

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

## <a name="in-keyword"></a>Parola chiave IN

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

## <a name="-operator"></a>* operatore

L'operatore speciale * proietta l'intero elemento come è. Quando usato, deve essere l'unico campo proiettato. Una query come `SELECT * FROM Families f` valido, ma `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` nejsou platné. Il [prima di tutto eseguire una query in questo articolo](#query-the-json-items) utilizzato la * operatore. 

## <a name="-and--operators"></a>? e?? Operatori

È possibile usare il Ternary (?) e Coalesce (?) gli operatori per compilare espressioni condizionali, come in linguaggi di programmazione quali C# e JavaScript. 

È possibile usare il? operatore per creare nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica i livelli di categoria nella `elementary` o `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

È inoltre possibile annidare le chiamate al? operatore, come nella query seguente: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Come con altri operatori di query, il? operatore esclude gli elementi se le proprietà di cui si fa riferimento sono mancante o i tipi confrontati sono diversi.

Uso di?? operatore per verificare se una proprietà in un elemento quando si eseguono query su dati semistrutturati o di tipo misto. Ad esempio, la query seguente restituisce `lastName` se presente, oppure `surname` se `lastName` non è presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Operatore TOP

La parola chiave TOP restituisce il primo `N` numero dei risultati della query in un ordine non definito. Come procedura consigliata, utilizzare TOP con la clausola ORDER BY per limitare i risultati al primo `N` numero di valori ordinati. La combinazione di questi due clausole è l'unico modo per indicare in modo prevedibile che le righe principali influisce.

È possibile utilizzare TOP con un valore costante, come nell'esempio seguente, o con un valore della variabile usando le query con parametri. Per altre informazioni, vedere la [le query con parametri](#parameterized-queries) sezione.

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

## <a id="OrderByClause"></a>Clausola ORDER BY

Come in SQL ANSI, è possibile includere una clausola ORDER BY facoltativa nelle query. L'argomento ASC o DESC facoltativo specifica se recuperare i risultati in ordine crescente o decrescente. Centro sicurezza di AZURE è quello predefinito.

Ad esempio, ecco una query che recupera le famiglie in ordine crescente di nome della città di residenza:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

I risultati sono:

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

La query seguente recupera famiglia `id`s nell'ordine della loro data di creazione di elementi. Elemento `creationDate` è un numero che rappresenta il *come valore epoch time*, o tempo trascorso dopo il 1 gennaio 1970 in secondi.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

I risultati sono:

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

Inoltre, è possibile ordinare dal più proprietà. Una query che Ordina dal più proprietà richiede un [indice composto](index-policy.md#composite-indexes). Considerare la query seguente:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Questa query recupera la famiglia `id` in ordine crescente del nome della città. Se più elementi hanno lo stesso nome di città, la query verrà ordinare in base il `creationDate` in ordine decrescente.

## <a id="OffsetLimitClause"></a>Clausola OFFSET limite

LIMITE di OFFSET è una clausola facoltativa per ignorare quindi richiedere un numero di valori dalla query. Il numero OFFSET e il conteggio di limite sono necessari nella clausola OFFSET limite.

Quando il limite di OFFSET viene utilizzato in combinazione con una clausola ORDER BY, il set di risultati viene generato eseguendo skip e accettano i valori ordinati. Se non esiste una clausola ORDER BY viene utilizzata, si verificherà in un ordine deterministico dei valori.

Ad esempio, ecco una query che ignora il primo valore e restituisce il secondo valore (in ordine di nome della città di residenza):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Ecco una query che ignora il primo valore e restituisce il secondo valore (senza ordinamento):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Espressioni scalari

La clausola SELECT supporta espressioni scalari, ad esempio le costanti, espressioni aritmetiche e le espressioni logiche. La query seguente usa un'espressione scalare:


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

## <a name="object-and-array-creation"></a>Creazione di oggetti e matrici

Una funzionalità chiave dell'API SQL è la creazione di matrice e oggetto. L'esempio precedente ha creato un nuovo oggetto JSON, `AreFromSameCityState`. È possibile creare matrici, come illustrato nell'esempio seguente:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

I risultati sono:

```json
    [
      {
        "CityState": [
          "Seattle",
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

La query SQL seguente è un altro esempio di utilizzo di matrice all'interno di nelle sottoquery. Questa query recupera tutti i nomi specificati distinti di elementi figlio in una matrice.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>Iterazione

L'API SQL offre il supporto per l'iterazione nelle matrici JSON, con un nuovo costrutto di aggiunte tramite la parola chiave nell'origine FROM. Nell'esempio seguente:

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

La query seguente esegue l'iterazione su `children` nella `Families` contenitore. Matrice di output è diversa dalla query precedente. Questo esempio suddivide `children`e converte i risultati in una matrice:  

```sql
    SELECT *
    FROM c IN Families.children
```

I risultati sono:

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

È possibile filtrare ulteriormente ciascuna voce individuale della matrice, come illustrato nell'esempio seguente:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

I risultati sono:

```json
    [{
      "givenName": "Lisa"
    }]
```

È anche possibile aggregare il risultato di un'iterazione della matrice. Ad esempio, la query seguente conta il numero di elementi figlio tutte le famiglie:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

I risultati sono:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Join

In un database relazionale, i join tra le tabelle sono il corollario logico della progettazione di schemi normalizzati. Al contrario, l'API SQL utilizza un modello dati denormalizzato di elementi privi di schema, che è logico equivalente di un *self join*.

Il linguaggio supporta la sintassi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Questa query restituisce un set di tuple con `N` valori. Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set. In altri termini, questa query restituisce il prodotto incrociato completo dei set che partecipano al join.

Gli esempi seguenti illustrano il funzionamento della clausola JOIN. Nell'esempio seguente, il risultato è vuoto, perché il prodotto incrociato di ogni elemento di origine e un set vuoto è vuoto:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Il risultato è:

```json
    [{
    }]
```

Nell'esempio seguente, il join è un prodotto incrociato tra due oggetti JSON, l'elemento radice `id` e il `children` sottoradice. Il fatto che `children` è una matrice non è efficace nel join, poiché deve gestire con una singola radice che è il `children` matrice. Il risultato contiene solo due risultati, perché il prodotto incrociato di ogni elemento con la matrice produce esattamente un solo elemento.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

I risultati sono:

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

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

I risultati sono:

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

L'origine FROM della clausola JOIN è un iteratore. Pertanto, il flusso nell'esempio precedente è:  

1. Espandere ciascun elemento figlio `c` nella matrice.
2. Applicare un prodotto incrociato con la radice dell'elemento `f` con ogni elemento figlio `c` che il primo passaggio resi bidimensionali.
3. Infine, proiettare l'oggetto radice `f` `id` solo la proprietà.

Il primo elemento `AndersenFamily`, contiene una sola `children` elemento, in modo che il set di risultati contiene solo un singolo oggetto. La seconda voce `WakefieldFamily`, contiene due `children`, in modo che il prodotto incrociato genera due oggetti, uno per ogni `children` elemento. I campi radice in entrambi gli elementi sono uguali, proprio come ci si aspetterebbe in un prodotto incrociato.

La vera utilità della clausola JOIN consiste nel formare tuple dal prodotto incrociato in una forma che altrimenti sarebbe difficile proiettare. L'esempio seguente i filtri alla combinazione di una tupla che consente all'utente di scegliere una condizione soddisfatta dall'insieme delle tuple complessiva.

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

I risultati sono:

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

L'estensione seguente dell'esempio precedente esegue un join di double. È possibile visualizzare il prodotto incrociato come lo pseudo-codice seguente:

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

`AndersenFamily` ha un figlio che ha un animale domestico, in modo che il prodotto incrociato genera una riga (1\*1\*1) da questa famiglia. `WakefieldFamily` ha due figli, solo uno dei quali ha animali domestici, ma tale elemento figlio ha però due animali. Il prodotto incrociato per questa famiglia restituisce 1\*1\*2 = 2 righe.

Nel prossimo esempio, è connesso un filtro supplementare `pet`, che consente di escludere tutte le tuple in cui il nome dell'animale non `Shadow`. È possibile creare tuple da matrici, filtro su uno degli elementi della tupla e proiettare qualsiasi combinazione degli elementi.

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

I risultati sono:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Funzioni definite dall'utente (UDF)

L'API SQL offre il supporto per le funzioni definite dall'utente (UDF). Con funzioni scalari definite dall'utente, è possibile passare zero o più argomenti e restituiscono un risultato singolo argomento. L'API verifica ogni argomento di essere valore JSON legale.  

L'API estende la sintassi SQL per il supporto per la logica dell'applicazione personalizzata tramite funzioni definite dall'utente. È possibile registrare funzioni definite dall'utente con l'API SQL e farvi riferimento nella query SQL. In effetti, le UDF sono progettate espressamente per essere chiamate dalle query. A corollario, funzioni definite dall'utente non ha accesso all'oggetto di contesto, come altri tipi di JavaScript, ad esempio stored procedure e trigger. Le query sono di sola lettura e possono eseguire in repliche primarie o secondarie. Funzioni definite dall'utente, a differenza di altri tipi di JavaScript, progettate per l'esecuzione nelle repliche secondarie.

Nell'esempio seguente registra una funzione definita dall'utente in un contenitore di elementi nel database di Cosmos DB. Nell'esempio viene creata una funzione definita dall'utente il cui nome è `REGEX_MATCH`. Accetta due valori di stringa JSON, `input` e `pattern`, e controlla se i primi corrisponde al modello specificato nel secondo utilizzo di JavaScript `string.match()` (funzione).

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

A questo punto, è possibile usare questa UDF in una proiezione di query. Funzioni definite dall'utente è necessario qualificare con il prefisso distinzione maiuscole/minuscole `udf.` durante la chiamata dall'interno delle query.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

I risultati sono:

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

È possibile usare la funzione definita dall'utente qualificato con il `udf.` prefisso all'interno di un filtro, come nell'esempio seguente:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

I risultati sono:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In pratica, le UDF sono espressioni scalari valide che è possibile usare in entrambe le proiezioni e filtri.

Per ampliare la potenza delle UDF, esaminiamo un altro esempio con la logica condizionale:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
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

Nell'esempio seguente usa la funzione definita dall'utente:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

I risultati sono:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se la proprietà definita per la funzione definita dall'utente i parametri non sono disponibili nel valore JSON, il parametro verrà considerato come undefined e la chiamata di funzione definita dall'utente è stata ignorata. Analogamente, se il risultato della UDF è undefined, non è incluso nel risultato.

Come mostrano negli esempi precedenti, le UDF integrano la potenza del linguaggio JavaScript con l'API SQL. Funzioni definite dall'utente forniscono un'interfaccia programmabile avanzata per eseguire operazioni complessa logica condizionale e procedurale con l'aiuto del runtime JavaScript integrate. L'API SQL fornisce gli argomenti alle UDF per ogni elemento di origine nel server di corrente WHERE o clausola SELECT fase dell'elaborazione. Il risultato è perfettamente incorporato nella pipeline di esecuzione complessivo. In breve, le UDF sono un ottimo strumento per eseguire la logica di business complessa come parte delle query.

## <a id="Aggregates"></a>Funzioni di aggregazione

Le funzioni di aggregazione eseguono un calcolo su un set di valori nella clausola SELECT e restituiscono un valore singolo. Ad esempio, la query seguente restituisce il conteggio degli elementi all'interno di `Families` contenitore:

```sql
    SELECT COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [{
        "$1": 2
    }]
```

È inoltre possibile restituire solo il valore scalare dell'aggregazione usando la parola chiave VALUE. Ad esempio, la query seguente restituisce il numero di valori come un singolo numero:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [ 2 ]
```

È anche possibile combinare le aggregazioni con i filtri. Ad esempio, la query seguente restituisce il numero di elementi con lo stato di indirizzo della `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

I risultati sono:

```json
    [ 1 ]
```

L'API SQL supporta le seguenti funzioni di aggregazione. SUM e AVG SQRT a valori numerici, e COUNT, MIN e MAX funzionano in numeri, stringhe, valori booleani e valori null.

| Funzione | Descrizione |
|-------|-------------|
| NUMERO | Restituisce il numero di elementi nell'espressione. |
| SUM   | Restituisce la somma dei valori nell'espressione. |
| MIN   | Restituisce il valore minimo nell'espressione. |
| MAX   | Restituisce il valore massimo nell'espressione. |
| MEDIO   | Restituisce la media dei valori nell'espressione. |

È anche possibile aggregare i risultati di un'iterazione della matrice. Per altre informazioni, vedere la [iterazione](#Iteration) sezione.

> [!NOTE]
> In Esplora dati del portale di Azure, le query di aggregazione possono aggregare risultati parziali pagina solo una query. il SDK produce un singolo valore cumulativo in tutte le pagine. Per eseguire query di aggregazione tramite codice, è necessario .NET SDK 1.12.0, .NET Core SDK 1.1.0 o SDK per Java 1.9.5 o versione successiva.
>

## <a id="BuiltinFunctions"></a>Funzioni predefinite

COSMOS DB supporta anche una serie di funzioni predefinite per le operazioni comuni che è possibile usare all'interno di query come le funzioni definite dall'utente (UDF).

| Gruppo di funzioni | Operazioni |
|---------|----------|
| Funzioni matematiche | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funzioni di controllo di tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funzioni stringa | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funzioni di matrice | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funzioni spaziali | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se attualmente si usa una funzione definita dall'utente (UDF) per i quali è ora disponibile una funzione predefinita, la corrispondente funzione predefinita sarà più rapido per l'esecuzione ed efficiente.

La differenza principale tra le funzioni di Cosmos DB e funzioni ANSI SQL è che le funzioni di Cosmos DB sono progettate per funzionare bene con i dati senza schema e con schema misto. Ad esempio, se una proprietà manca o con un valore non numerico come `unknown`, l'elemento viene ignorato invece di restituire un errore.

### <a name="mathematical-functions"></a>Funzioni matematiche

Le funzioni matematiche eseguono un calcolo basato su valori di input passati come argomenti e restituiscono un valore numerico. Di seguito è riportata una tabella delle funzioni matematiche predefinite supportate.

| Uso | Descrizione |
|----------|--------|
| ABS (num_expr) | Restituisce il valore assoluto (positivo) dell'espressione numerica specificata. |
| CEILING (num_expr) | Restituisce il più piccolo valore integer maggiore di o uguale all'espressione numerica specificata. |
| FLOOR (num_expr) | Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata. |
| EXP (num_expr) | Restituisce l'esponente dell'espressione numerica specificata. |
| LOG (num_expr, base) | Restituisce il logaritmo naturale dell'espressione numerica specificata oppure il logaritmo usando la base specificata. |
| LOG10 (num_expr) | Restituisce il valore logaritmico in base 10 dell'espressione numerica specificata. |
| ROUND (num_expr) | Restituisce un valore numerico, arrotondato al valore integer più vicino. |
| TRUNC (num_expr) | Restituisce un valore numerico, troncato al valore integer più vicino. |
| SQRT (num_expr) | Restituisce la radica quadrata dell'espressione numerica specificata. |
| SQUARE (num_expr) | Restituisce il quadrato dell'espressione numerica specificata. |
| POWER (num_expr, num_expr) | Restituisce la potenza dell'espressione numerica specificata al valore specificato. |
| SIGN (num_expr) | Restituisce il valore del segno (-1, 0, 1) dell'espressione numerica specificata. |
| ACOS (num_expr) | Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno. |
| ASIN (num_expr) | Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Questa funzione è detta anche arcoseno. |
| ATAN (num_expr) | Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Questa funzione viene chiamata anche arcotangente. |
| ATN2 (num_expr) | Restituisce l'angolo, espresso in radianti, tra l'asse x positivo e il raggio dall'origine al punto (y, x), dove x e y sono i valori delle due espressioni float specificate. |
| COS (num_expr) | Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata. |
| COT (num_expr) | Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata. |
| GRADI (num_expr) | Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti. |
| PI () | Restituisce il valore costante di pi greco. |
| RADIANTI (num_expr) | Restituisce radianti quando viene immessa un'espressione numerica, espresso in gradi. |
| SIN (num_expr) | Restituisce il seno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata. |
| TAN (num_expr) | Restituisce la tangente dell'espressione di input nell'espressione specificata. |

È possibile eseguire query come nell'esempio seguente:

```sql
    SELECT VALUE ABS(-4)
```

Il risultato è:

```json
    [4]
```

### <a name="type-checking-functions"></a>Funzioni di controllo di tipo

Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione all'interno di una query SQL. È possibile usare funzioni di controllo di tipo per determinare i tipi di proprietà all'interno di elementi in tempo reale, quando si trovano variabile o sconosciuto. Ecco una tabella di funzioni di controllo del tipo supportate predefinite:

| **Utilizzo** | **Descrizione** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Restituisce un valore booleano che indica se il tipo del valore è una matrice. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Restituisce un valore booleano che indica se il tipo del valore è booleano. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Restituisce un valore booleano che indica se il tipo del valore è Null. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Restituisce un valore booleano che indica se il tipo del valore è un numero. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Restituisce un valore booleano che indica se il tipo del valore è un oggetto JSON. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Restituisce un valore booleano che indica se il tipo del valore è una stringa. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Restituisce un valore booleano che indica se il tipo del valore è una stringa, numero, booleano o null. |

Usando queste funzioni, è possibile eseguire query come nell'esempio seguente:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Il risultato è:

```json
    [true]
```

### <a name="string-functions"></a>Funzioni stringa

Le funzioni scalari seguenti eseguono un'operazione su un valore di input stringa e restituiscono un valore stringa, numerici o booleani. Ecco una tabella di funzioni per stringhe:

| Uso | Descrizione |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Restituisce il numero di caratteri dell'espressione stringa specificata. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Restituisce parte di un'espressione stringa. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Restituisce la posizione iniziale della prima occorrenza della seconda stringa di espressione all'interno, la prima espressione stringa specificata oppure -1 se non viene trovata la stringa. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Restituisce la parte sinistra di una stringa con il numero specificato di caratteri. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Restituisce la parte destra di una stringa con il numero specificato di caratteri. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Restituisce un'espressione stringa dopo la rimozione di tutti gli spazi vuoti finali. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Ripete un valore stringa in un numero di volte specificato. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Restituisce l'inverso di un valore stringa. |

Usando queste funzioni, è possibile eseguire query simile alla seguente, che restituisce la famiglia `id` in lettere maiuscole:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

I risultati sono:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

In alternativa, la concatenazione di stringhe, ad esempio in questo esempio:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

I risultati sono:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

È anche possibile usare funzioni di stringa nella clausola WHERE per filtrare i risultati, come nell'esempio seguente:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

I risultati sono:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funzioni di matrice

Le funzioni scalari seguenti eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o valore di matrice. La tabella seguente include funzioni di matrice predefinite:

| Uso | Descrizione |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Restituisce il numero di elementi dell'espressione di matrice specificato. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Restituisce una matrice che rappresenta il risultato della concatenazione di due o più valori della matrice. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Restituisce un valore booleano che indica se la matrice contiene il valore specificato. Può specificare se la corrispondenza è completa o parziale. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Restituisce parte di un'espressione di matrice. |

Usare funzioni di matrice per manipolare le matrici in JSON. Ad esempio, ecco una query che restituisce tutti gli elementi `id`s dove un del `parents` è `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Il risultato è:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

È possibile specificare un frammento parziale per la corrispondenza di elementi all'interno della matrice. La query seguente trova tutti gli elementi `id`che hanno `parents` con il `givenName` di `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Il risultato è:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Ecco un altro esempio che usa ARRAY_LENGTH per ottenere il numero di `children` per ogni famiglia:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

I risultati sono:

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

COSMOS DB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali: 

| Uso | Descrizione |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Restituisce la distanza tra i due GeoJSON `Point`, `Polygon`, o `LineString` espressioni. |
| T_WITHIN (point_expr, polygon_expr) | Restituisce un'espressione booleana che indica se il primo oggetto GeoJSON (`Point`, `Polygon`, o `LineString`) si trova all'interno del secondo oggetto GeoJSON (`Point`, `Polygon`, o `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Restituisce un'espressione booleana che indica se i due valori oggetti GeoJSON specificati (`Point`, `Polygon`, o `LineString`) si intersecano. |
| ST_ISVALID | Restituisce un valore booleano che indica se l'oggetto GeoJSON specificata `Point`, `Polygon`, o `LineString` espressione è valida. |
| ST_ISVALIDDETAILED | Restituisce un valore JSON che contiene un valore booleano se l'oggetto GeoJSON specificata `Point`, `Polygon`, o `LineString` espressione è valida e se non è valido, il motivo come valore stringa. |

È possibile utilizzare le funzioni spaziali per eseguire query di prossimità rispetto ai dati spaziali. Ad esempio, ecco una query che restituisce tutti gli elementi della famiglia entro 30 km di una posizione specificata utilizzando la funzione predefinita ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Il risultato è:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Per altre informazioni sul supporto geospaziale in Cosmos DB, vedere [Uso dei dati geospaziali in Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Query con parametri

COSMOS DB supporta le query con parametri espressi dalla consueta notazione @. SQL con parametri fornisce alla gestione affidabile ed escape dell'input dell'utente e impedisce l'esposizione accidentale dei dati mediante attacchi SQL injection.

Ad esempio, è possibile scrivere una query che accetta `lastName` e `address.state` come parametri ed eseguirlo per diversi valori di `lastName` e `address.state` basate sull'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

È quindi possibile inviare la richiesta a Cosmos DB come query con parametri JSON simile al seguente:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

L'esempio seguente imposta l'argomento principale con una query con parametri: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

I valori dei parametri possono essere qualsiasi valore JSON valido: stringhe, numeri, valori booleani, null, persino matrici o una stringa JSON nidificata. Poiché Cosmos DB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.

## <a id="JavaScriptIntegration"></a>Integrazione JavaScript

Azure Cosmos DB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nei contenitori, utilizzando stored procedure e trigger. Questo modello supporta:

* Operazioni CRUD transazionali con prestazioni elevate e query su elementi in un contenitore, grazie alla stretta integrazione del runtime JavaScript all'interno del motore di database.
* Modellazione naturale del flusso di controllo, variabili di ambito e assegnazione e integrazione delle primitive di gestione delle eccezioni con transazioni di database. 

Per altre informazioni sull'integrazione di Azure Cosmos DB JavaScript, vedere la [API lato server JavaScript](#JavaScriptServerSideApi) sezione.

### <a name="operator-evaluation"></a>Valutazione degli operatori

COSMOS DB, poiché è un database JSON, disegna con gli operatori JavaScript e semantica di valutazione. COSMOS DB provi a mantenere la semantica di JavaScript in termini di supporto JSON, ma la valutazione dell'operazione devia in alcune istanze.

L'API SQL, a differenza di tradizionale linguaggio SQL, i tipi di valori sono spesso non noti fino a quando l'API consente di recuperare i valori dal database. Per poter eseguire le query in maniera efficiente, gran parte degli operatori ha rigorosi requisiti di tipi.

A differenza di JavaScript, l'API SQL non esegue conversioni implicite. Ad esempio, una query come `SELECT * FROM Person p WHERE p.Age = 21` corrisponde a elementi che contengono un' `Age` proprietà il cui valore `21`. Non corrisponde ad alcun altro elemento la cui proprietà `Age` corrisponde al parametro possibili variazioni, ad esempio `twenty-one`, `021`, o `21.0`. Questo contrasta con JavaScript, in cui i valori stringa vengono eseguito il cast implicito di numeri in base all'operatore, ad esempio: `==`. Questo comportamento di API SQL è fondamentale per la ricerca di indici corrispondenti.

## <a id="ExecutingSqlQueries"></a>Esecuzione di query SQL

Qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS può chiamare l'API REST di Cosmos DB. COSMOS DB offre anche librerie di programmazione per linguaggi di programmazione .NET, Node. js, JavaScript e Python. L'API REST e librerie di supportano delle query tramite SQL e .NET SDK supporta anche [l'esecuzione di query LINQ](#Linq).

Negli esempi seguenti viene illustrato come creare una query e inviarla a fronte di un account di database di Cosmos DB.

### <a id="RestAPI"></a>API REST

Cosmos DB offre un modello di programmazione aperto RESTful su HTTP. Il modello di risorsa è costituito da un set di risorse in un account di database che effettua il provisioning una sottoscrizione di Azure. L'account del database è costituito da un set di *database*, ognuna delle quali può contenere più *contenitori*, che a sua volta contenere *elementi*, funzioni definite dall'utente e altri tipi di risorse. Ogni risorsa di Cosmos DB è indirizzabile tramite un URI logico e stabile. Un set di risorse viene chiamato un *feed*. 

È il modello di interazione di base con queste risorse Usa i verbi HTTP `GET`, `PUT`, `POST`, e `DELETE`, con le interpretazioni standard. Usare `POST` per creare una nuova risorsa, eseguire una stored procedure oppure eseguire una query di Cosmos DB. Le query sono sempre operazioni di sola lettura senza nessun effetto collaterale.

Gli esempi seguenti illustrano un `POST` per una query dell'API SQL rispetto agli elementi di esempio. La query include un semplice filtro nel codice JSON `name` proprietà. Il `x-ms-documentdb-isquery` e Content-Type: `application/query+json` intestazioni indicano che l'operazione è una query. Sostituire `mysqlapicosmosdb.documents.azure.com:443` con l'URI per l'account Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
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

I risultati sono:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

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
                "city":"Seattle"
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

La query successiva e più complessa restituisce più risultati da un join:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
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

I risultati sono: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

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

Se i risultati della query non possono essere contenuta in un'unica pagina, l'API REST restituisce un token di continuazione attraverso il `x-ms-continuation-token` intestazione della risposta. I client possono impaginare i risultati includendo l'intestazione nei risultati successivi. È anche possibile controllare il numero di risultati per pagina tramite il `x-ms-max-item-count` intestazione dei numeri. 

Se una query contiene una funzione di aggregazione, ad esempio COUNT, la pagina di query può restituire un valore parzialmente aggregato su solo una pagina di risultati. I client devono eseguire un'aggregazione di secondo livello su questi risultati per produrre i risultati finali. Ad esempio sommare i conteggi restituiti nelle singole pagine per ottenere il conteggio totale.

Per gestire i criteri di coerenza dei dati per le query, usare il `x-ms-consistency-level` intestazione come in tutte le richieste API REST. La coerenza di sessione richiede anche la versione più recente di ripetizione `x-ms-session-token` intestazione cookie nella richiesta di query. I criteri di indicizzazione del contenitore sulla quale è stata eseguita la query possono influenzare anche la coerenza dei risultati della query. Con il valore predefinito delle impostazioni di criteri per i contenitori di indicizzazione, l'indice è sempre aggiornato con il contenuto di elemento e i risultati della query corrispondono alla coerenza scelta per i dati. Per altre informazioni, vedere [livelli di coerenza di Azure Cosmos DB][consistency-levels].

Se i criteri di indicizzazione configurati per il contenitore non possono supportare la query specificata, il server di Azure Cosmos DB restituisce 400 "richiesta non valida". Questo messaggio di errore restituisce per le query con i percorsi esplicitamente esclusi dall'indicizzazione. È possibile specificare il `x-ms-documentdb-query-enable-scan` intestazione per consentire alla query eseguire una scansione quando un indice non è disponibile.

È possibile ottenere metriche dettagliate sull'esecuzione di query impostando il `x-ms-documentdb-populatequerymetrics` intestazione `true`. Per altre informazioni, vedere [metriche di query SQL per Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK supporta l'esecuzione di query LINQ ed SQL. Nell'esempio seguente viene illustrato come eseguire la query del filtro precedente con .NET:

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

Nell'esempio seguente vengono confrontate due proprietà per verificarne l'uguaglianza all'interno di ogni elemento e proiezioni anonime.

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

L'esempio seguente vengono illustrati i join, esplicito tramite LINQ `SelectMany`.

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

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Il client .NET esegue automaticamente l'iterazione attraverso tutte le pagine dei risultati della query nella `foreach` si blocca, come illustrato nell'esempio precedente. Le opzioni di query introdotte nel [API REST](#RestAPI) sezione sono disponibili anche in .NET SDK, usando la `FeedOptions` e `FeedResponse` le classi nel `CreateDocumentQuery` (metodo). È possibile controllare il numero di pagine tramite la `MaxItemCount` impostazione.

È possibile controllare in modo esplicito il paging creando `IDocumentQueryable` usando il `IQueryable` oggetto, quindi leggendo il `ResponseContinuationToken` i valori e passarli nuovamente come `RequestContinuationToken` in `FeedOptions`. È possibile impostare `EnableScanInQuery` per abilitare l'analisi quando la query non è supportata da criteri di indicizzazione configurati. Per i contenitori partizionati, è possibile usare `PartitionKey` per eseguire la query a fronte di una partizione singola, anche se Azure Cosmos DB può estrazione automatica dal testo della query. È possibile usare `EnableCrossPartitionQuery` per eseguire query su più partizioni.

Per altri esempi di .NET con le query, vedere la [esempi di Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) in GitHub.

### <a id="JavaScriptServerSideApi"></a>API lato server JavaScript

COSMOS DB offre un modello di programmazione per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nei contenitori, utilizzando stored procedure e trigger. La logica JavaScript registrata a livello di contenitore può quindi rilasciare operazioni di database per gli elementi del contenitore specificato, incapsulati nelle transazioni ACID ambient.

Nell'esempio seguente viene illustrato come utilizzare `queryDocuments` nel server di JavaScript API per eseguire query dall'interno di stored procedure e trigger:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>Da LINQ all'API SQL

LINQ è un modello di programmazione .NET che esprime il calcolo come query su flussi oggetto. Cosmos DB fornisce una libreria lato client che si interfaccia con LINQ agevolando una conversione tra oggetti JSON e .NET e un mapping da un sottoinsieme di query LINQ alle query di Cosmos DB.

Il diagramma seguente mostra l'architettura di supporto di query LINQ usando Cosmos DB. Con il client di Cosmos DB, è possibile creare un `IQueryable` oggetto direttamente il provider di query di Cosmos DB e traduce la query LINQ in una query di Cosmos DB. Passare quindi la query al server di Cosmos DB, che recupera un set di risultati in formato JSON. Il deserializzatore JSON converte i risultati in un flusso di oggetti .NET sul lato client.

![Architettura di supporto delle query LINQ usando API SQL - sintassi SQL, linguaggio di query JSON, concetti relativi ai database e query SQL][1]

### <a name="net-and-json-mapping"></a>Mapping .NET e JSON

Il mapping tra oggetti .NET e gli elementi JSON è naturale. Ogni campo del membro dati viene eseguito il mapping a un oggetto JSON, in cui il nome del campo viene eseguito il mapping per il *chiave* esegue il mapping a parte dell'oggetto e il valore in modo ricorsivo il *valore* fa parte dell'oggetto. Il codice seguente esegue il mapping di `Family` classe a un elemento JSON e quindi crea un `Family` oggetto:

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

L'esempio precedente crea l'elemento JSON seguente:

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

Il provider di query di Cosmos DB esegue con il massimo impegno un mapping da una query LINQ a una query SQL di Cosmos DB. La descrizione seguente presuppone una conoscenza di base con LINQ.

Il sistema di tipi di provider di query supporta solo i tipi primitivi JSON: numerico, booleano, stringa e null. 

Il provider di query supporta le seguenti espressioni scalari:

- Valori costanti, inclusi i valori costanti dei tipo di dati primitivi al momento della valutazione di query.
  
- Espressioni di indice di matrice/di proprietà che fanno riferimento alla proprietà di un oggetto o un elemento della matrice. Ad esempio:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Espressioni aritmetiche, tra cui espressioni aritmetiche comuni su valori numerici e booleani. Per un elenco completo, vedere la [specifica di SQL di Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Espressioni di confronto stringa, che includono il confronto di un valore stringa su un valore stringa costante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Espressioni di creazione oggetto/matrice, che restituiscono un oggetto di tipo valore composito o tipo anonimo o una matrice di tali oggetti. È possibile annidare questi valori.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Operatori LINQ supportati

Il provider LINQ incluso in SQL .NET SDK supporta gli operatori seguenti:

- **Select**: Le proiezioni convertono in SQL SELECT, inclusa la costruzione di oggetti.
- **Where**: I filtri vengono convertite in SQL WHERE e supportano la conversione tra `&&`, `||`, e `!` agli operatori di SQL
- **SelectMany**: consente la rimozione di matrici nella clausola SQL JOIN. Consente di concatenare o annidare le espressioni per filtrare gli elementi della matrice.
- **OrderBy** e **OrderByDescending**: Vengono convertite in ORDER BY con ASC o DESC.
- Operatori di aggregazione **Count**, **Sum**, **Min**, **Max** e **Average** e relativi equivalenti asincroni **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: trasla in confronti di intervallo. Comunemente utilizzato per le stringhe, poiché non sono confrontabili in .NET.
- **Take**: Si traduce in SQL TOP per limitare i risultati da una query.
- **Funzioni matematiche**: Supporta la conversione da .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, e `Truncate` funzioni predefinite di SQL equivalenti.
- **Funzioni stringa**: Supporta la conversione da .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, e `TrimStart` funzioni predefinite di SQL equivalenti.
- **Le funzioni di matrice**: Supporta la conversione da .NET `Concat`, `Contains`, e `Count` funzioni predefinite di SQL equivalenti.
- **Le funzioni geospaziali estensione**: Supporta la conversione dai metodi sthub `Distance`, `IsValid`, `IsValidDetailed`, e `Within` funzioni predefinite di SQL equivalenti.
- **Funzione di estensione della funzione definita dall'utente**: Supporta la conversione dal metodo stub `UserDefinedFunctionProvider.Invoke` alla funzione corrispondente definita dall'utente.
- **Miscellaneous**: Supporta la conversione di `Coalesce` e agli operatori condizionali. Può essere convertita `Contains` in String CONTAINS, ARRAY_CONTAINS o IN SQL, in base al contesto.

### <a name="sql-query-operators"></a>Operatori di query SQL

Gli esempi seguenti illustrano come alcuni degli operatori di query LINQ standard vengono convertite in query di Cosmos DB.

#### <a name="select-operator"></a>Seleziona operatore

La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Operatore Select, nell'esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selezionare l'operatore di esempio 2:** 

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selezionare l'operatore di esempio 3:**

- **Espressione lambda LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>Operatore SelectMany

La sintassi è `input.SelectMany(x => f(x))`, dove `f` è un'espressione scalare che restituisce un tipo di contenitore.

- **Espressione lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Operatore Where

La sintassi è `input.Where(x => f(x))`, dove `f` è un'espressione scalare che restituisce un valore booleano.

**In cui operatore, ad esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**In cui operatore, ad esempio 2:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Query SQL composte

È possibile comporre gli operatori precedenti per formare query più avanzate. Poiché Cosmos DB supporta contenitori annidati, è possibile concatenare o annidare la composizione.

#### <a name="concatenation"></a>Concatenazione

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata può iniziare con facoltativo `SelectMany` query, seguita da più `Select` o `Where` operatori.

**Concatenazione, esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concatenazione, esempio 2:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concatenazione, esempio 3:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concatenazione, esempio 4:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Annidamento

La sintassi è `input.SelectMany(x=>x.Q())` in cui `Q` è un `Select`, `SelectMany`, o `Where` operatore.

Una query annidata applica la query interna a ogni elemento del contenitore esterno. Una funzionalità importante è che la query interna può riferirsi ai campi degli elementi nel contenitore esterno, ad esempio un self join.

**Annidamento, esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Annidamento, esempio 2:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Annidamento, esempio 3:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Riferimenti

- [Specifica SQL di Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Specifiche JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Valutazione tecniche di query per database di grandi dimensioni](https://dl.acm.org/citation.cfm?id=152611). *ACM Computing Surveys* 25, nessuna. 2 (1993).
- Graefe, G. "Il framework di unarie a catena per l'ottimizzazione delle query." *Specifica i dati IEEE Tendente al rialzo.* 18, no. 3 (1995).
- Lu, Ooi, Tan. "Query Processing in sistemi di Database relazionali parallele". *IEEE Computer Society Press* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar e Andrew Tomkins. "Pig Latin: Un linguaggio non così estraneo per l'elaborazione dei dati." *SIGMOD* (2008).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB][introduction]
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Livelli di coerenza di Azure Cosmos DB][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
