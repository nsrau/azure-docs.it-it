---
title: Conversione LINQ to SQL in Azure Cosmos DB
description: Informazioni gli operatori LINQ supportati e il modo in cui le query LINQ vengono mappate alle query SQL in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441262"
---
# <a name="linq-to-sql-translation"></a>Traduzione LINQ in SQL

Il provider di query Cosmos DB di Azure esegue il mapping del lavoro migliore da una query LINQ in una query SQL Cosmos DB. La descrizione seguente presuppone una familiarità di base con LINQ.

Il sistema di tipi del provider di query supporta solo i tipi primitivi JSON: numerico, booleano, stringa e null.

Il provider di query supporta le espressioni scalari seguenti:The query provider supports the following scalar expressions:

- Valori costanti, inclusi i valori costanti dei tipi di dati primitivi in fase di valutazione delle query.
  
- Espressioni di indice di proprietà/matrice che fanno riferimento alla proprietà di un oggetto o di un elemento della matrice. Ad esempio:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Espressioni aritmetiche, incluse espressioni aritmetiche comuni su valori numerici e booleani. Per l'elenco completo, vedere la specifica SQL del [database Cosmos di Azure](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Espressioni di confronto tra stringhe, che includono il confronto di un valore stringa con un valore stringa costante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Espressioni di creazione oggetto/matrice, che restituiscono un oggetto di tipo valore composto o anonimo, o una matrice di tali oggetti. È possibile nidificare questi valori.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Operatori LINQ supportati

Il provider LINQ incluso in SQL .NET SDK supporta gli operatori seguenti:

- **Selezionare**: le proiezioni vengono convertite in SQL SELECT, inclusa la costruzione di oggetti.
- **Where**: I filtri vengono convertiti `&&` `||`in `!` SQL WHERE e supportano la conversione tra , e negli operatori SQL
- **SelectMany**: consente la rimozione di matrici nella clausola SQL JOIN. Utilizzare per concatenare o annidare le espressioni per filtrare gli elementi della matrice.
- **OrderBy** e **OrderByDescending**: Traduci in ORDER BY con ASC o DESC.
- Operatori di aggregazione **Count**, **Sum**, **Min**, **Max** e **Average** e relativi equivalenti asincroni **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: converte in confronti di intervallo. Comunemente utilizzati per le stringhe, dal momento che non sono confrontabili in .NET.
- **Skip** and **Take**: converte in SQL OFFSET e LIMIT per limitare i risultati di una query ed eseguire l'impaginazione.
- **Funzioni matematiche**: supporta `Abs`la `Acos` `Asin`conversione da .NET , `Round` `Sign`, `Sin` `Sqrt`, `Tan`, `Truncate` `Atan` `Ceiling` `Cos` `Exp` `Floor`, `Log`, , `Log10`, `Pow`, , , , , e alle funzioni predefinite SQL equivalenti.
- **Funzioni stringa**: Supporta la `Concat` `Contains`conversione da .NET `ToLower`, `ToUpper` `TrimEnd`, `Count` `TrimStart` , , `EndsWith``IndexOf`, `Replace` `Reverse`, `StartsWith` `SubString`, , , , e alle funzioni predefinite SQL equivalenti.
- **Funzioni di**matrice : Supporta `Concat` `Contains`la `Count` conversione da .NET , e alle funzioni predefinite SQL equivalenti.
- **Funzioni di estensione geospaziale**: supporta `Distance` `IsValid`la `IsValidDetailed`conversione dai metodi stub , , e `Within` alle funzioni predefinite SQL equivalenti.
- **Funzione di estensione**della funzione definita dall'utente `UserDefinedFunctionProvider.Invoke` : supporta la conversione dal metodo stub alla funzione definita dall'utente corrispondente.
- **Varie**: Supporta la conversione `Coalesce` e gli operatori condizionali. Può `Contains` tradursi in stringa CONTAINS, ARRAY_CONTAINS o SQL IN, a seconda del contesto.

## <a name="examples"></a>Esempi

Negli esempi seguenti viene illustrato come alcuni degli operatori di query LINQ standard vengono convertiti in query Cosmos DB.

### <a name="select-operator"></a>Seleziona operatore

La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Selezionare operatore, esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selezionare operatore, esempio 2:** 

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selezionare operatore, esempio 3:**

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

### <a name="selectmany-operator"></a>Operatore SelectMany

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

### <a name="where-operator"></a>Operatore Where

La sintassi è `input.Where(x => f(x))`, dove `f` è un'espressione scalare che restituisce un valore booleano.

**Dove operatore, esempio 1:**

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
  
**Dove operatore, esempio 2:**

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

## <a name="composite-sql-queries"></a>Query SQL composte

È possibile comporre gli operatori precedenti per formare query più potenti. Poiché Cosmos DB supporta contenitori nidificati, è possibile concatenare o nidificare la composizione.

### <a name="concatenation"></a>Concatenazione

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata può `SelectMany` iniziare con `Select` una `Where` query facoltativa, seguita da più o operatori.

**Concatenazione, esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
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

### <a name="nesting"></a>Annidamento

La sintassi `Q` è `Select` `input.SelectMany(x=>x.Q())` `SelectMany`where `Where` è un operatore , o .

Una query annidata applica la query interna a ogni elemento del contenitore esterno. Una caratteristica importante è che la query interna può fare riferimento ai campi degli elementi nel contenitore esterno, ad esempio un self-join.

**Nidificazione, esempio 1:**

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

**Nidificazione, esempio 2:**

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

**Nidificazione, esempio 3:**

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


## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modellare i dati del documento](modeling-data.md)
