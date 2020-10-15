---
title: Funzioni di data e ora nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL di data e ora in Azure Cosmos DB per eseguire operazioni DateTime e timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605183"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funzioni di data e ora (Azure Cosmos DB)

Le funzioni di data e ora consentono di eseguire operazioni DateTime e timestamp in Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funzioni per ottenere la data e l'ora

Le funzioni scalari seguenti consentono di ottenere la data e l'ora UTC correnti in tre forme, ovvero una stringa conforme al formato ISO 8601, un timestamp numerico il cui valore è il numero di millisecondi trascorsi dal periodo UNIX o i cicli numerici il cui valore è il numero di cicli di 100 nanosecondi trascorsi dal periodo UNIX :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funzioni da usare con i valori DateTime

Le funzioni seguenti consentono di modificare con facilità i valori DateTime, timestamp e di selezione:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
