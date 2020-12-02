---
title: Usare le etichette delle query in sinapsi SQL
description: Le informazioni contenute in questo articolo sono suggerimenti essenziali per l'uso delle etichette di query in sinapsi SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 59fa68d12f1d8be598810399fc5623c2af983979
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462245"
---
# <a name="use-query-labels-in-synapse-sql"></a>Usare le etichette delle query in sinapsi SQL

Le informazioni contenute in questo articolo sono suggerimenti essenziali per l'uso delle etichette di query in sinapsi SQL.

> [!NOTE]
> Il pool SQL senza server non supporta l'assegnazione di etichette alle query.

## <a name="what-are-query-labels"></a>Cosa sono le etichette delle query

Il pool SQL dedicato supporta un concetto denominato etichette di query. Prima di approfondire il concetto, eccone un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Questo tag è utile perché l'etichetta è in grado di eseguire query tramite il DMV. L'esecuzione di query per le etichette fornisce un meccanismo per individuare le query sui problemi e consente di identificare lo stato di avanzamento tramite un'esecuzione ELT.

Sono molto utili le convenzioni di denominazione ottimali. Ad esempio, se si avvia l'etichetta con PROJECT, PROCEDURE, istruzione o commento, viene identificata in modo univoco la query tra tutto il codice nel controllo del codice sorgente.

Nella query seguente viene utilizzata una vista a gestione dinamica per eseguire la ricerca in base all'etichetta:

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
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).


