---
title: Funzioni di data e ora nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL di data e ora in Azure Cosmos DB per eseguire operazioni DateTime e timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224952"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funzioni di data e ora (Azure Cosmos DB)

Le funzioni di data e ora consentono di eseguire operazioni DateTime e timestamp in Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funzioni per ottenere la data e l'ora

Le funzioni scalari seguenti consentono di ottenere la data e l'ora UTC correnti in due formati: una stringa conforme al formato ISO 8601 o un timestamp numerico il cui valore è l'epoch di UNIX in millisecondi:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funzioni da usare con i valori DateTime

Le funzioni seguenti consentono di modificare facilmente i valori DateTime:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
