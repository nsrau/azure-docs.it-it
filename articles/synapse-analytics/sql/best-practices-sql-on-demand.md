---
title: Procedure consigliate per SQL su richiesta (anteprima) in Azure Synapse AnalyticsBest practices for SQL on-demand (preview) in Azure Synapse Analytics
description: Consigli e procedure consigliate da conoscere quando si lavora con SQL su richiesta (anteprima).
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429070"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Procedure consigliate per SQL su richiesta (anteprima) in Azure Synapse AnalyticsBest practices for SQL on-demand (preview) in Azure Synapse Analytics

In questo articolo è presente una raccolta di procedure consigliate per l'utilizzo di SQL su richiesta (anteprima). SQL on-demand is an additional resource within Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure.SQL on-demand allows you to query files in your Azure storage accounts. Non dispone di funzionalità di archiviazione o inserimento locali. Di conseguenza, tutti i file con destinazioni di query sono esterni a SQL su richiesta. Tutto ciò che riguarda la lettura di file dall'archiviazione potrebbe avere un impatto sulle prestazioni delle query.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocalizzare l'account di archiviazione di Azure e SQL su richiestaColocate Azure Storage account and SQL on-demand

Per ridurre al minimo la latenza, spostare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint di cui è stato eseguito il provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. Se non si trovano nella stessa area, si aumenterà la latenza per il trasferimento di rete dei dati tra le aree remote e dell'endpoint.

## <a name="azure-storage-throttling"></a>Limitazione di Archiviazione di AzureAzure Storage throttling

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione dello storage si verifica quando le operazioni di I/O al secondo o velocità effettiva combinate generate da applicazioni, servizi e carico di lavoro SQL su richiesta superano i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni delle query.

Una volta rilevata la limitazione delle richieste, SQL on-demand include la gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste.

> [!TIP]
> Per un'esecuzione ottimale delle query, non eseguire lo stress dell'account di archiviazione con altri carichi di lavoro durante l'esecuzione della query.

## <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di queryPrepare files for querying

Se possibile, è possibile preparare i file per migliorare le prestazioni:

- Converti CSV in parquet - Parquet è in formato colonnare. Dal momento che è compresso, le sue dimensioni di file sono più piccole dei file CSV con gli stessi dati. SQL su richiesta richiederà meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, sarà possibile suddividerlo in più file più piccoli.
- Prova a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o una tabella esterna LOCATION.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: [selezionare le funzioni nome file e percorso file per utilizzare partizioni specifiche.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per assegnare partizioni specificheUse fileinfo and filepath functions to target specific partitions

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. Questa funzione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che otterrai prestazioni migliori.

Per ulteriori informazioni, controllare le funzioni [del nome del file](develop-storage-files-overview.md#filename-function) e del percorso del [file](develop-storage-files-overview.md#filepath-function) ed esempi su come eseguire query su [file specifici.](query-specific-files.md)

Se i dati archiviati non sono partizionati, prendere in considerazione il partizionamento in modo da poter utilizzare queste funzioni per ottimizzare le query destinate a tali file. Quando si [esegue una query sulle tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà automaticamente destinata solo ai file necessari.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare il CETAS per migliorare le prestazioni delle query e i joinUse CETAS to enhance query performance and joins

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL on-demand. CETAS è un'operazione parallela che crea metadati di tabella esterna ed esporta i risultati della query SELECT in un set di file nell'account di archiviazione.

È possibile utilizzare CETAS per archiviare parti di query usate di frequente, ad esempio tabelle di riferimento unite in join, in un nuovo set di file. Successivamente, è possibile eseguire il join a questa singola tabella esterna anziché ripetere join comuni in più query.

Poiché CETAS genera file di parquet, le statistiche verranno create automaticamente quando la prima query è destinata a questa tabella esterna, con conseguente miglioramento delle prestazioni.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo [Risoluzione dei problemi](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e soluzioni comuni. Se si utilizza il pool SQL anziché SQL su richiesta, vedere l'articolo [Procedure consigliate per il pool SQL](best-practices-sql-pool.md) per indicazioni specifiche.
