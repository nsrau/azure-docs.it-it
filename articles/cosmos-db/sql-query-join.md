---
title: SQL JOIN queries for Azure Cosmos DB
description: Informazioni su come JOIN di più tabelle in Azure Cosmos DB per eseguire query sui dati
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871143"
---
# <a name="joins-in-azure-cosmos-db"></a>Join in Azure Cosmos DB

In un database relazionale, i join tra tabelle sono il corollario logico per la progettazione di schemi normalizzati. Al contrario, l'API SQL utilizza il modello di dati denormalizzato degli elementi privi di schema, che è l'equivalente logico di un *self-join.*

Gli inner join generano un prodotto incrociato completo dei set che partecipano al join. Il risultato di un join a N vie è un set di tuple a N elementi, in cui ogni valore presente nella tupla è associato al set con alias che partecipa al join e l'accesso al set è possibile facendo riferimento a tale alias in altre clausole.

## <a name="syntax"></a>Sintassi

Il linguaggio supporta `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`la sintassi . Questa query restituisce un `N` set di tuple con valori. Ogni tupla ha valori prodotti dall'iterazione di tutti gli alias del contenitore sui rispettivi set. 

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

Gli esempi seguenti illustrano il funzionamento della clausola JOIN. Prima di eseguire questi esempi, caricare i dati della [famiglia](sql-query-getting-started.md#upload-sample-data)di esempio . Nell'esempio seguente, il risultato è vuoto, poiché il prodotto incrociato di ogni elemento dall'origine e un set vuoto è vuoto:

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

Nell'esempio seguente, il join è un prodotto incrociato `id` tra `children` due oggetti JSON, la radice dell'elemento e la sottodirectory radice. Il fatto `children` che è una matrice non è efficace nel join, perché `children` si occupa di una singola radice che è la matrice. Il risultato contiene solo due risultati, perché il prodotto incrociato di ogni elemento con la matrice produce esattamente un solo elemento.

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

L'origine FROM della clausola JOIN è un iteratore. Quindi, il flusso nell'esempio precedente è:  

1. Espandere ogni `c` elemento figlio nella matrice.
2. Applicare un prodotto incrociato con `f` la radice `c` dell'elemento con ogni elemento figlio appiattito dal primo passaggio.
3. Infine, proiettare `f` `id` la sola proprietà dell'oggetto radice.

Il primo `AndersenFamily`elemento, , `children` , contiene un solo elemento, pertanto il set di risultati contiene un solo oggetto. Il secondo `WakefieldFamily`elemento, `children`, contiene due , in modo `children` che il prodotto incrociato produca due oggetti, uno per ogni elemento. I campi radice in entrambi gli elementi sono uguali, proprio come ci si aspetterebbe in un prodotto incrociato.

La vera utilità della clausola JOIN consiste nel formare tuple dal prodotto incrociato in una forma altrimenti difficile da proiettare. L'esempio seguente filtra la combinazione di una tupla che consente all'utente di scegliere una condizione soddisfatta dalle tuple nel complesso.

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

L'estensione seguente dell'esempio precedente esegue un doppio join. È possibile visualizzare il prodotto incrociato come il seguente pseudo-codice:

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

`AndersenFamily`ha un bambino che ha un animale domestico, quindi\*\*il prodotto incrociato produce una fila (1 1 1) da questa famiglia. `WakefieldFamily`ha due figli, solo uno dei quali ha animali domestici, ma quel bambino ha due animali domestici. Il prodotto incrociato per\*questa\*famiglia produce 1 1 2 x 2 righe.

Nell'esempio successivo è presente un `pet`filtro aggiuntivo su , che esclude tutte `Shadow`le tuple in cui il nome dell'animale domestico non è . È possibile compilare tuple da matrici, filtrare in base a uno qualsiasi degli elementi della tupla e proiettare qualsiasi combinazione di elementi.

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
- [Sottoquery:](sql-query-subquery.md)