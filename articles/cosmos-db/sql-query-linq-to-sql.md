---
title: LINQ to SQL traduzione in Azure Cosmos DB
description: Mapping di query LINQ a Azure Cosmos DB query SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ce9d96a90a2463d1ab8e1a9774a019e38ca681f4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036026"
---
# <a name="linq-to-sql-translation"></a>Traduzione LINQ in SQL

Il provider di query di Azure Cosmos DB esegue un mapping ottimale da una query LINQ in una query di Cosmos DB SQL. La descrizione seguente presuppone una conoscenza di base di LINQ.

Il sistema di tipi del provider di query supporta solo i tipi primitivi JSON: numeric, Boolean, String e null.

Il provider di query supporta le seguenti espressioni scalari:

- Valori costanti, inclusi i valori costanti dei tipi di dati primitivi al momento della valutazione della query.
  
- Espressioni di indice di proprietà/matrici che fanno riferimento alla proprietà di un oggetto o di un elemento di matrice. Esempio:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Espressioni aritmetiche, incluse le espressioni aritmetiche comuni sui valori numerici e booleani. Per l'elenco completo, vedere la [specifica Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Espressioni di confronto tra stringhe, che includono il confronto di un valore di stringa con un valore stringa costante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Espressioni di creazione di oggetti/matrici, che restituiscono un oggetto di tipo di valore composto o di tipo anonimo o una matrice di tali oggetti. È possibile annidare questi valori.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Operatori LINQ supportati

Il provider LINQ incluso in SQL .NET SDK supporta gli operatori seguenti:

- **Select**: Le proiezioni vengono convertite in SQL SELECT, inclusa la costruzione di oggetti.
- **Where**: I filtri vengono convertiti in SQL dove e supportano `&&`la `||`conversione tra `!` , e per gli operatori SQL
- **SelectMany**: consente la rimozione di matrici nella clausola SQL JOIN. Usare per concatenare o annidare espressioni per filtrare gli elementi della matrice.
- **OrderBy** e **OrderByDescending**: Tradurre in ORDER BY con ASC o DESC.
- Operatori di aggregazione **Count**, **Sum**, **Min**, **Max** e **Average** e relativi equivalenti asincroni **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: trasla in confronti di intervallo. Utilizzato comunemente per le stringhe, poiché non sono confrontabili in .NET.
- **Take**: Viene convertito in SQL TOP per limitare i risultati di una query.
- **Funzioni matematiche**: Supporta la conversione da `Abs`.NET `Acos`, `Asin`, `Atan` ,,`Cos` ,,`Exp`, ,`Log`, ,,`Pow` `Floor` `Ceiling` `Log10` `Round`, `Sign`, ,`Sin` ,`Tan`enelle funzioni predefinite di SQLequivalenti.`Truncate` `Sqrt`
- **Funzioni stringa**: Supporta la conversione da `Concat`.NET `Contains`, `Count`, `EndsWith` ,,`Replace` ,,`Reverse`, ,`SubString`, ,,`ToUpper` `StartsWith``IndexOf` `ToLower` `TrimEnd` e`TrimStart` alle funzioni predefinite di SQL equivalenti.
- **Funzioni di matrice**: Supporta la conversione da `Concat`.NET `Contains`, e `Count` alle funzioni predefinite di SQL equivalenti.
- **Funzioni di estensione**geospaziale: Supporta la conversione da metodi `Distance`Stub `IsValid` `IsValidDetailed`,, e `Within` alle funzioni predefinite di SQL equivalenti.
- **Funzione di estensione della funzione definita dall'utente**: Supporta la conversione dal metodo `UserDefinedFunctionProvider.Invoke` Stub alla funzione definita dall'utente corrispondente.
- **Miscellaneous**: Supporta la conversione `Coalesce` di operatori condizionali e. Può tradurre `Contains` in stringa Contains, ARRAY_CONTAINS o SQL in, a seconda del contesto.

## <a name="examples"></a>Esempi

Negli esempi seguenti viene illustrato il modo in cui alcuni degli operatori di query LINQ standard vengono convertiti in Cosmos DB query.

### <a name="select-operator"></a>Seleziona operatore

La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Operatore Select, esempio 1:**

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Operatore Select, esempio 2:** 

- **Espressione lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Operatore Select, esempio 3:**

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

**Operatore Where, esempio 1:**

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
  
**Operatore Where, esempio 2:**

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

È possibile comporre gli operatori precedenti per formare query più potenti. Poiché Cosmos DB supporta i contenitori annidati, è possibile concatenare o annidare la composizione.

### <a name="concatenation"></a>Concatenazione

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata può iniziare con una `SelectMany` query facoltativa, seguita `Select` da `Where` più operatori OR.

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

La sintassi è `input.SelectMany(x=>x.Q())` dove `Q` è un `Select`operatore `SelectMany`, o `Where` .

Una query nidificata applica la query interna a ogni elemento del contenitore esterno. Una funzionalità importante è che la query interna può fare riferimento ai campi degli elementi nel contenitore esterno, ad esempio un self-join.

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
