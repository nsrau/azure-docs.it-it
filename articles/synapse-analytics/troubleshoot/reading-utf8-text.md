---
title: 'Risoluzione dei problemi: lettura di testo UTF-8 da file CSV o PARQUET usando un pool SQL senza server'
description: Lettura di testo UTF-8 da file CSV o PARQUET usando un pool SQL senza server in Azure sinapsi Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466425"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Risolvere i problemi di lettura del testo UTF-8 da file CSV o parquet usando un pool SQL senza server in Azure sinapsi Analytics

Questo articolo illustra la procedura di risoluzione dei problemi per la lettura di testo UTF-8 da file CSV o parquet usando un pool SQL senza server in Azure sinapsi Analytics.

Quando il testo UTF-8 viene letto da un file CSV o PARQUET usando un pool SQL senza server, alcuni caratteri speciali come ü e ö vengono convertiti erroneamente se la query restituisce colonne VARCHAR con regole di confronto non UTF8. Si tratta di un problema noto in SQL Server e in Azure SQL. Le regole di confronto non UTF8 sono l'impostazione predefinita in sinapsi SQL, in modo che le query dei clienti saranno interessate. I clienti che usano caratteri inglesi standard e alcuni subset di caratteri latini estesi potrebbero non notare gli errori di conversione. La conversione non corretta è illustrata in modo più dettagliato in [utilizzare sempre le regole di confronto UTF-8 per leggere il testo UTF-8 nel pool SQL senza server](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Soluzione alternativa

La soluzione alternativa a questo problema consiste nell'usare sempre le regole di confronto UTF-8 per la lettura di testo UTF-8 da file CSV o PARQUET.

-   In molti casi, è sufficiente impostare le regole di confronto UTF8 nel database (operazione dei metadati).
-   Se non sono state specificate regole di confronto UTF8 in tabelle esterne che leggono dati UTF8, è necessario ricreare le tabelle esterne interessate e impostare le regole di confronto UTF8 sulle colonne VARCHAR (operazione dei metadati).


## <a name="next-steps"></a>Passaggi successivi

* [CETAS con Synapse SQL](../sql/develop-tables-cetas.md)
* [Avvio rapido: Usare i pool SQL serverless](../quickstart-sql-on-demand.md)
