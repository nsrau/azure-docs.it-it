---
title: Uso delle etichette per instrumentare le query
description: Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692830"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Uso delle etichette per instrumentare query in Azure SQL Data Warehouse
Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni.


## <a name="what-are-labels"></a>Definizione di etichette
SQL Data Warehouse supporta un concetto detto etichette di query. Prima di approfondire il concetto, eccone un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Questo tag è particolarmente utile in quanto l'etichetta suppor la query tramite le DMV. L'esecuzione di query per le etichette offre un meccanismo per l'individuazione di query problematiche e semplifica il controllo dell'avanzamento mediante l'esecuzione di un processo ELT.

Una buona convenzione di denominazione è estremamente utile. Ad esempio, una stringa che inizia con PROJECT, PROCEDURE, STATEMENT o COMMENT può facilitare l'identificazione univoca della query in tutto il codice nel controllo del codice sorgente.

La query seguente usa una vista a gestione dinamica da sottoporre a ricerca in base all'etichetta.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> È essenziale racchiudere tra parentesi quadre o virgolette doppie la parola label durante l'esecuzione della query. Label è una parola riservata e restituisce un errore quando non è delimitata. 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).


