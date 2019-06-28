---
title: Query SQL JOIN per Azure Cosmos DB
description: Informazioni sulla sintassi JOIN SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342854"
---
# <a name="joins-in-azure-cosmos-db"></a>Crea un join in Azure Cosmos DB

In un database relazionale, i join tra le tabelle sono il corollario logico della progettazione di schemi normalizzati. Al contrario, l'API SQL utilizza un modello dati denormalizzato di elementi privi di schema, che è logico equivalente di un *self join*.

Gli inner join generano un prodotto incrociato completo dei set che partecipano al join. Il risultato di un join a N vie è un set di tuple a N elementi, in cui ogni valore presente nella tupla è associato al set con alias che partecipa al join e l'accesso al set è possibile facendo riferimento a tale alias in altre clausole.

## <a name="syntax"></a>Sintassi

Il linguaggio supporta la sintassi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Questa query restituisce un set di tuple con `N` valori. Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set. 

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

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Sottoquery](sql-query-subquery.md)