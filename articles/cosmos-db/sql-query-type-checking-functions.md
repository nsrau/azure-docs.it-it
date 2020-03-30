---
title: Funzioni di controllo dei tipi nel linguaggio di query del database Cosmos di AzureType checking functions in Azure Cosmos DB query language
description: Informazioni sul controllo dei tipi delle funzioni di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349072"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funzioni di controllo dei tipi (Azure Cosmos DB)Type checking functions (Azure Cosmos DB)

Le funzioni di controllo dei tipi consentono di controllare il tipo di un'espressione all'interno di una query SQL. È possibile utilizzare le funzioni di controllo dei tipi per determinare i tipi di proprietà all'interno degli elementi in tempo reale, quando sono variabili o sconosciuti. 

## <a name="functions"></a>Funzioni

Ecco una tabella delle funzioni di controllo dei tipi incorporate supportate:Here's a table of supported built-in type-checking functions:

Le funzioni seguenti supportano il controllo del tipo per i valori di input e ogni funzione restituisce un valore booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
