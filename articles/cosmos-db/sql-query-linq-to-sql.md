---
title: Traduzione LINQ in SQL in Azure Cosmos DB
description: Mapping di query LINQ alle query SQL di Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342510"
---
# <a name="linq-to-sql-translation"></a>Traduzione LINQ in SQL

Il provider di query di Azure Cosmos DB esegue il mapping di un impegno più di una query LINQ in una query SQL di Cosmos DB. La descrizione seguente presuppone una conoscenza di base con LINQ.

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

## <a id="SupportedLinqOperators"></a>Operatori LINQ supportati

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

## <a name="examples"></a>Esempi

Gli esempi seguenti illustrano come alcuni degli operatori di query LINQ standard vengono convertite in query di Cosmos DB.

### <a name="select-operator"></a>Operatore Select

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

## <a name="composite-sql-queries"></a>Query SQL composte

È possibile comporre gli operatori precedenti per formare query più avanzate. Poiché Cosmos DB supporta contenitori annidati, è possibile concatenare o annidare la composizione.

### <a name="concatenation"></a>Concatenazione

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

### <a name="nesting"></a>Annidamento

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


## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dati del documento modello](modeling-data.md)
