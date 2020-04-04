---
title: Utilizzo delle etichette per instrumentare le query
description: Suggerimenti per l'utilizzo di etichette per instrumentare query nel pool Synapse SQL per lo sviluppo di soluzioni.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633499"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Utilizzo di etichette per instrumentare query nel pool SQL SynapseUsing labels to instrument queries in Synapse SQL pool

In questo articolo sono inclusi suggerimenti per lo sviluppo di soluzioni che usano le etichette per instrumentare le query nel pool SQL.

Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="what-are-labels"></a>Definizione di etichette

Il pool SQL supporta un concetto denominato etichette di query. Prima di approfondire il concetto, eccone un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Questo tag è utile perché l'etichetta è in grado di eseguire query tramite le DMV.

L'esecuzione di query per le etichette offre un meccanismo per l'individuazione di query problematiche e semplifica il controllo dell'avanzamento mediante l'esecuzione di un processo ELT.

Una buona convenzione di denominazione è estremamente utile. Ad esempio, l'avvio dell'etichetta con PROJECT, PROCEDURE, STATEMENT o COMMENT identifica in modo univoco la query tra tutto il codice nel controllo del codice sorgente.

La query seguente utilizza una vista a gestione dinamica per eseguire la ricerca in base all'etichetta:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> È essenziale racchiudere tra parentesi quadre o virgolette doppie la parola label durante l'esecuzione della query. Label è una parola riservata e restituisce un errore quando non è delimitata.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
