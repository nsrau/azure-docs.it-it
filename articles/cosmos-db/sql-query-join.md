---
title: Query di JOIN SQL per Azure Cosmos DB
description: Informazioni sulla sintassi SQL di JOIN per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: d78904fde53da0e800a69d2148a9c4e3acf57307
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494411"
---
# <a name="joins-in-azure-cosmos-db"></a>Join in Azure Cosmos DB

In un database relazionale, i join tra le tabelle sono il corollario logico della progettazione di schemi normalizzati. Al contrario, l'API SQL utilizza il modello di dati denormalizzato di elementi privi di schema, che è l'equivalente logico di un *self-join*.

Gli inner join generano un prodotto incrociato completo dei set che partecipano al join. Il risultato di un join a N vie è un set di tuple a N elementi, in cui ogni valore presente nella tupla è associato al set con alias che partecipa al join e l'accesso al set è possibile facendo riferimento a tale alias in altre clausole.

## <a name="syntax"></a>Sintassi

Il linguaggio supporta la sintassi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Questa query restituisce un set di tuple con valori `N`. Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set. 

Verrà ora esaminata la seguente clausola FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Consentire a ogni origine di definire `input_alias1, input_alias2, …, input_aliasN`. Questa clausola FROM restituisce un set di N tuple (tuple con N valori). Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set.  
  
**Esempio 1** -2 origini  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source2>` di avere un ambito documento che fa riferimento a input_alias1 e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2>` produrrà le tuple seguenti:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Esempio 2** -3 origini  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source2>` di avere un ambito documento che fa riferimento `input_alias1` e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- Consentire a `<from_source3>` di avere un ambito documento che fa riferimento `input_alias2` e di rappresentare i set:  
  
    {100, 200} per `input_alias2 = 1,`  
  
    {300} per `input_alias2 = 3,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` produrrà le tuple seguenti:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Mancano le tuple per altri valori di `input_alias1`, `input_alias2`, per cui `<from_source3>` non ha restituito alcun valore.  
  
**Esempio 3** -3 origini  
  
- Consentire a <from_source1> di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a `<from_source1>` di avere un ambito contenitore e di rappresentare il set {A, B, C}.  
  
- Consentire a <from_source2> di avere un ambito documento che fa riferimento a input_alias1 e di rappresentare i set:  
  
    {1, 2} per `input_alias1 = A,`  
  
    {3} per `input_alias1 = B,`  
  
    {4, 5} per `input_alias1 = C,`  
  
- Consentire a `<from_source3>` di avere ambito `input_alias1` e rappresentare i set:  
  
    {100, 200} per `input_alias2 = A,`  
  
    {300} per `input_alias2 = C,`  
  
- La clausola FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` produrrà le tuple seguenti:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > Viene generato un prodotto incrociato tra `<from_source2>` e `<from_source3>` perché entrambi hanno come ambito lo stesso elemento `<from_source1>`.  Sono state create 4 (2x2) tuple con valore A, 0 tuple con valore B (1 x 0) e 2 (2x1) tuple con valore C.  
  
## <a name="examples"></a>Esempi

Gli esempi seguenti illustrano il funzionamento della clausola JOIN. Prima di eseguire questi esempi, caricare i [dati della famiglia](sql-query-getting-started.md#upload-sample-data)di esempio. Nell'esempio seguente, il risultato è vuoto, perché il prodotto incrociato di ogni elemento dall'origine e un set vuoto è vuoto:

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

Nell'esempio seguente, il join è un prodotto incrociato tra due oggetti JSON, la radice dell'elemento `id` e il `children` sottoradice. Il fatto che `children` sia una matrice non è efficace nel join, perché tratta di una singola radice che corrisponde alla matrice di `children`. Il risultato contiene solo due risultati, perché il prodotto incrociato di ogni elemento con la matrice restituisce esattamente un solo elemento.

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

L'origine FROM della clausola JOIN è un iteratore. Il flusso nell'esempio precedente è quindi:  

1. Espandere ogni elemento figlio `c` nella matrice.
2. Applicare un prodotto incrociato con la radice dell'elemento `f` con ogni elemento figlio `c` il primo passaggio è bidimensionale.
3. Infine, proiettare l'oggetto radice `f` `id` solo la proprietà.

Il primo elemento, `AndersenFamily`, contiene un solo elemento `children`, quindi il set di risultati contiene solo un singolo oggetto. Il secondo elemento, `WakefieldFamily`, contiene due `children`, quindi il prodotto incrociato produce due oggetti, uno per ogni elemento `children`. I campi radice in entrambi gli elementi sono uguali, proprio come ci si aspetterebbe in un prodotto incrociato.

L'utilità reale della clausola JOIN consiste nel formare Tuple dal prodotto incrociato in una forma altrimenti difficile da proiettare. Nell'esempio seguente viene filtrata la combinazione di una tupla che consente all'utente di scegliere una condizione soddisfatta dalle tuple complessive.

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

Nell'estensione seguente dell'esempio precedente viene eseguito un doppio join. È possibile visualizzare il prodotto incrociato come lo pseudo-codice seguente:

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

`AndersenFamily` ha un figlio con un solo animale, quindi il prodotto incrociato restituisce una riga (1\*1\*1) da questa famiglia. `WakefieldFamily` dispone di due figli, solo uno dei quali ha animali domestici, ma l'elemento figlio ha due animali domestici. Il prodotto incrociato per questa famiglia restituisce 1\*1\*2 = 2 righe.

Nell'esempio seguente è presente un filtro aggiuntivo in `pet`, che esclude tutte le tuple in cui il nome dell'animale domestico non è `Shadow`. È possibile compilare tuple da matrici, filtrare in base a qualsiasi elemento della tupla e proiettare qualsiasi combinazione degli elementi.

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

## <a name="next-steps"></a>Passaggi successivi

- [Attività iniziali](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Sottoquery](sql-query-subquery.md)