---
title: Usare le etichette di query in Synapse SQLUse query labels in Synapse SQL
description: In questo articolo sono inclusi suggerimenti essenziali per l'utilizzo di etichette di query in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430032"
---
# <a name="use-query-labels-in-synapse-sql"></a>Usare le etichette di query in Synapse SQLUse query labels in Synapse SQL
In questo articolo sono inclusi suggerimenti essenziali per l'utilizzo di etichette di query in Synapse SQL.

> [!NOTE]
> SQL on-demand (anteprima) non supporta le query di etichettatura.

## <a name="what-are-query-labels"></a>Che cosa sono le etichette di query
Il pool SQL supporta un concetto denominato etichette di query. Prima di approfondire il concetto, eccone un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Questo tag è particolarmente utile in quanto l'etichetta suppor la query tramite le DMV. L'esecuzione di query per le etichette fornisce un meccanismo per individuare le query di problema e consente di identificare lo stato di avanzamento tramite un'esecuzione ELT.

Una buona convenzione di denominazione è molto utile. Ad esempio, l'avvio dell'etichetta con PROJECT, PROCEDURE, STATEMENT o COMMENT identifica in modo univoco la query tra tutto il codice nel controllo del codice sorgente.

La query seguente utilizza una vista a gestione dinamica per eseguire la ricerca in base all'etichetta:

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


