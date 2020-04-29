---
title: Procedure consigliate per SQL su richiesta (anteprima) in Azure sinapsi Analytics
description: Suggerimenti e procedure consigliate che è opportuno tenere presente durante l'utilizzo di SQL su richiesta (anteprima).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429070"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Procedure consigliate per SQL su richiesta (anteprima) in Azure sinapsi Analytics

In questo articolo è presente una raccolta di procedure consigliate per l'uso di SQL su richiesta (anteprima). SQL su richiesta è una risorsa aggiuntiva in Azure sinapsi Analytics.

## <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non dispone di funzionalità di archiviazione locale o di inserimento. Di conseguenza, tutti i file di destinazione della query sono esterni a SQL su richiesta. Tutti gli elementi correlati alla lettura di file dall'archiviazione potrebbero avere un effetto sulle prestazioni di esecuzione delle query.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Trovare un account di archiviazione di Azure e SQL su richiesta

Per ridurre al minimo la latenza, colocare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. Se non si trovano nella stessa area, si verifica un aumento della latenza per il trasferimento di rete dei dati tra le aree remote e l'endpoint.

## <a name="azure-storage-throttling"></a>Limitazione di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione dell'archiviazione si verifica quando le operazioni di i/o al secondo e la velocità effettiva combinate generate da applicazioni, servizi e carichi di lavoro SQL su richiesta superano i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni di esecuzione delle query.

Una volta rilevata la limitazione, SQL su richiesta dispone di una gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste.

> [!TIP]
> Per un'esecuzione ottimale delle query, non è necessario sottoporre a stress l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query

## <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, è possibile preparare i file per ottenere prestazioni migliori:

- Convertire CSV in parquet-parquet è un formato a colonne. Poiché è compresso, le dimensioni del file sono inferiori ai file CSV con gli stessi dati. SQL su richiesta richiede meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile suddividerla in più file più piccoli.
- Provare a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile disporre di file di dimensioni uguali per un singolo percorso OPENROWSET o una posizione di tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: selezionare [Usa funzioni filename e FilePath per individuare partizioni specifiche](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni FileInfo e FilePath per individuare partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile richiedere a SQL su richiesta di eseguire query su file e cartelle specifiche. Questa funzione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che potrai ottenere prestazioni migliori.

Per altre informazioni, vedere funzioni [filename](develop-storage-files-overview.md#filename-function) e [filePath](develop-storage-files-overview.md#filepath-function) ed esempi su come [eseguire una query su file specifici](query-specific-files.md).

Se i dati archiviati non sono partizionati, provare a partizionarlo per poter usare queste funzioni per ottimizzare le query destinate a tali file. Quando si [eseguono query su tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà destinata automaticamente solo ai file necessari.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per migliorare le prestazioni di esecuzione delle query e i join

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati della tabella esterna ed Esporta i risultati della query SELECT in un set di file nell'account di archiviazione.

È possibile utilizzare CETAS per archiviare parti di query utilizzate di frequente, come le tabelle di riferimento Unite in join, a un nuovo set di file. A questo punto, è possibile aggiungere join a questa singola tabella esterna anziché ripetere i join comuni in più query.

Quando CETAS genera file parquet, le statistiche vengono create automaticamente quando la prima query è destinata a questa tabella esterna, con conseguente miglioramento delle prestazioni.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'articolo sulla [risoluzione dei](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) problemi e delle soluzioni comuni. Se si usa il pool SQL anziché SQL su richiesta, vedere l'articolo [procedure consigliate per il pool SQL](best-practices-sql-pool.md) per istruzioni specifiche.
