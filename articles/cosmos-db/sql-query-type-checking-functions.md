---
title: Funzioni di controllo dei tipi nel linguaggio di query Azure Cosmos DB
description: Informazioni sul controllo dei tipi funzioni di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 96fea7cb53c89c02583a6dba8434cd33641d6a09
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341502"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funzioni di controllo del tipo (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione all'interno di una query SQL. È possibile utilizzare le funzioni di controllo del tipo per determinare i tipi di proprietà all'interno di elementi in tempo reale, quando sono variabili o sconosciute. 

## <a name="functions"></a>Funzioni

Ecco una tabella delle funzioni di controllo dei tipi predefinite supportate:

Le funzioni seguenti supportano il controllo del tipo per i valori di input e ogni funzione restituisce un valore booleano.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
