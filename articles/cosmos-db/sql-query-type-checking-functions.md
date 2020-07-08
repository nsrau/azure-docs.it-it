---
title: Funzioni di controllo dei tipi nel linguaggio di query Azure Cosmos DB
description: Informazioni sul controllo dei tipi funzioni di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3ffe8fa9286c8594dc72d78582f8c7b0a3d05c78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563335"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funzioni di controllo del tipo (Azure Cosmos DB)

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
