---
title: Scenari di dati con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conoscere i diversi scenari e gli strumenti disponibili per inserire, elaborare, scaricare e visualizzare i dati in Data Lake Storage Gen2, noto in precedenza come Azure Data Lake Store
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: dfc47c40ce82eb8e9d414cb49c22bee033d000a6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239029"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data

L'elaborazione di Big Data prevede quattro fasi principali:

* Inserimento di grandi quantità di dati in un archivio dati, in tempo reale o in batch
* Elaborazione dei dati
* Download dei dati
* Visualizzazione dei dati

In questo articolo vengono descritte queste fasi in relazione ad Azure Data Lake Storage Gen2, per illustrare le opzioni e gli strumenti disponibili per soddisfare le esigenze di Big Data.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Inserire i dati in Data Lake Storage Gen2
Questa sezione descrive le diverse origini dei dati e i diversi modi in cui i dati possono essere inseriti in un account Data Lake Storage Gen2.

![Inserire i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Inserire i dati in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dati ad hoc
Si tratta di set di dati di piccole dimensioni usati per la creazione del prototipo di un'applicazione Big Data. L'inserimento di dati ad hoc può essere eseguito in modi diversi, a seconda dell'origine dei dati.

| origine dati | Inserire usando |
| --- | --- |
| Computer locale |<ul> <li>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| BLOB di Archiviazione di Azure |<ul> <li>[Data factory di Azure](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp in esecuzione nel cluster HDInsight](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dati di streaming
Si tratta dei dati che possono essere generati da origini diverse, ad esempio applicazioni, dispositivi, sensori, ecc. Questi dati possono essere inseriti in Data Lake Storage Gen2 con numerosi strumenti diversi. Questi strumenti in genere acquisiscono ed elaborano i dati dei singoli eventi in tempo reale e quindi scrivono gli eventi in batch in Data Lake Storage Gen2 perché possano essere elaborati ulteriormente.

Di seguito sono elencati gli strumenti che è possibile usare:

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md): è possibile scrivere i dati direttamente in Data Lake Storage Gen2 dal cluster Storm.

### <a name="relational-data"></a>Dati relazionali
È inoltre possibile recuperare i dati dai database relazionali. I database relazionali raccolgono nel tempo elevate quantità di dati che possono fornire informazioni significative se elaborate tramite una pipeline Big Data. Per spostare tali dati in Data Lake Storage Gen2, è possibile usare gli strumenti seguenti.

* [Data factory di Azure](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati di log del server Web (caricamento tramite applicazioni personalizzate)
Questo tipo di set di dati è indicato in modo specifico perché l'analisi dei dati di log dei server Web è un caso d'uso comune per le applicazioni di Big Data e richiede il caricamento di grandi volumi di file di log in Data Lake Storage Gen2. Per scrivere script o applicazioni per il caricamento dei dati è possibile usare uno degli strumenti seguenti.

* [Data factory di Azure](../../data-factory/copy-activity-overview.md)

Per il caricamento di dati di log del server Web e per il caricamento di altri tipi di dati, ad esempio i dati delle valutazioni dei social network, è consigliabile scrivere script o applicazioni personalizzate che offrono la flessibilità di includere il componente di caricamento dei dati come parte dell'applicazione Big Data più grande. In alcuni casi, questo codice può essere rappresentato da uno script o un'utilità della riga di comando. In altri casi, il codice può essere usato per integrare l'elaborazione di Big Data in un'applicazione o una soluzione aziendale.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati ai cluster Azure HDInsight
La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, Storm) supporta Data Lake Storage Gen2 come repository di archiviazione dei dati. I cluster HDInsight accedono ai dati dai BLOB di archiviazione di Azure (WASB). Per prestazioni ottimali, è possibile copiare i dati da WASB a un account Data Lake Storage Gen2 associato al cluster. Per copiare i dati è possibile usare gli strumenti seguenti.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [Strumento AzCopy](../common/storage-use-azcopy-v10.md)
* [Data factory di Azure](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dati archiviati in locale o in cluster IaaS Hadoop
Grandi quantità di dati possono essere archiviati in cluster Hadoop esistenti, localmente, nei computer che usano HDFS. I cluster Hadoop potrebbero essere in una distribuzione locale o all'interno di un cluster IaaS in Azure. Potrebbe essere necessario copiare tali dati in Azure Data Lake Storage Gen2 una sola volta o regolarmente. Sono disponibili diverse opzioni che è possibile usare per ottenere questo. Di seguito è riportato un elenco di alternative con i relativi compromessi.

| Approccio | Dettagli | Vantaggi | Considerazioni |
| --- | --- | --- | --- |
| Usare Azure Data Factory (ADF) per copiare i dati direttamente dai cluster Hadoop in Azure Data Lake Storage Gen2 |[ADF supporta HDFS come origine dati](../../data-factory/connector-hdfs.md) |ADF fornisce il supporto nativo per HDFS e il monitoraggio e gestione end-to-end di prima classe |Richiede che il gateway di gestione dati sia distribuito localmente nel cluster IaaS |
| Usare Distcp per copiare dati da Hadoop ad Archiviazione di Azure. Copiare quindi i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando un meccanismo appropriato. |È possibile copiare i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando: <ul><li>[Data factory di Azure](../../data-factory/copy-activity-overview.md)</li><li>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp Apache in esecuzione nei cluster HDInsight](data-lake-storage-use-distcp.md)</li></ul> |È possibile usare strumenti open source. |Processo in più passaggi che prevede più tecnologie |

### <a name="really-large-datasets"></a>Set di dati di grandi dimensioni
L'uso dei metodi descritti in precedenza per il caricamento di set di dati di più terabyte può talvolta risultare lento e costoso. In questi casi, è possibile usare le opzioni seguenti.

* **Uso di Azure ExpressRoute**. Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Ciò offre un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Elaborare i dati archiviati in Data Lake Storage Gen2
Quando i dati sono disponibili in Data Lake Storage Gen2, è possibile eseguire l'analisi sui dati tramite le applicazioni di Big Data supportate. Attualmente, è possibile usare Azure HDInsight e Azure Databricks per eseguire processi di analisi dei dati sui dati archiviati in Data Lake Storage Gen2.

![Analizzare i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizzare i dati in Data Lake Storage Gen2")


## <a name="download-data-from-data-lake-storage-gen2"></a>Scaricare i dati da Data Lake Storage Gen2
È anche possibile che si voglia scaricare o spostare i dati da Azure Data Lake Storage Gen2 per scenari simili ai seguenti:

* Spostare i dati in altri repository per l'interfaccia con le pipeline di elaborazione dati esistenti. È possibile, ad esempio, che sia necessario spostare i dati da Data Lake Storage Gen2 al database SQL di Azure o al server SQL locale.
* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Estrarre i dati da Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Estrarre i dati da Data Lake Storage Gen2")

In questi casi, è possibile usare le opzioni seguenti:

* [Data factory di Azure](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Visualizzare i dati in Data Lake Storage Gen2
È possibile usare una combinazione di servizi per creare rappresentazioni visive dei dati archiviati in Data Lake Storage Gen2.

![Visualizzare i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualizzare i dati in Data Lake Storage Gen2")

* Iniziare usando [Azure Data Factory per spostare i dati da Data Lake Storage Gen2 ad Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Successivamente è possibile [integrare Power BI con Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) per creare la rappresentazione visiva dei dati.
