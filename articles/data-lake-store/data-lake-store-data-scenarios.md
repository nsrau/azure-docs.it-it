---
title: Scenari di dati con Data Lake Storage Gen1 | Microsoft Docs
description: Conoscere i diversi scenari e gli strumenti disponibili per inserire, elaborare, scaricare e visualizzare i dati in Data Lake Storage Gen1, noto in precedenza come Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0b16154edbda4bedfd4e9b680ba4311e7a235212
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60879038"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Uso di Azure Data Lake Storage Gen1 per i requisiti di Big Data

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

L'elaborazione di Big Data prevede quattro fasi principali:

* Inserimento di grandi quantità di dati in un archivio dati, in tempo reale o in batch
* Elaborazione dei dati
* Download dei dati
* Visualizzazione dei dati

In questo articolo vengono descritte queste fasi in relazione ad Azure Data Lake Storage Gen1, per illustrare le opzioni e gli strumenti disponibili per soddisfare le esigenze di Big Data.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Inserire i dati in Data Lake Storage Gen1
Questa sezione descrive le diverse origini dei dati e i diversi modi in cui i dati possono essere inseriti in un account Data Lake Storage Gen1.

![Inserire i dati in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Inserire i dati in Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dati ad hoc
Si tratta di set di dati di piccole dimensioni usati per la creazione del prototipo di un'applicazione Big Data. L'inserimento di dati ad hoc può essere eseguito in modi diversi, a seconda dell'origine dei dati.

| origine dati | Inserire usando |
| --- | --- |
| Computer locale |<ul> <li>[Portale di Azure](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usare Strumenti di Data Lake per Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| BLOB di Archiviazione di Azure |<ul> <li>[Data factory di Azure](../data-factory/connector-azure-data-lake-store.md)</li> <li>[strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp in esecuzione nel cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dati di streaming
Si tratta dei dati che possono essere generati da origini diverse, ad esempio applicazioni, dispositivi, sensori, ecc. Questi dati possono essere inseriti in Data Lake Storage Gen1 con numerosi strumenti diversi. Questi strumenti in genere acquisiscono ed elaborano i dati dei singoli eventi in tempo reale e quindi scrivono gli eventi in batch in Data Lake Storage Gen1 affinché possano essere elaborati ulteriormente.

Di seguito sono elencati gli strumenti che è possibile usare:

* [Analisi di flusso di Azure](../stream-analytics/stream-analytics-data-lake-output.md): gli eventi inseriti in Hub eventi possono essere scritti in Azure Data Lake Storage Gen1 usando un output di Azure Data Lake Storage Gen1.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md): è possibile scrivere i dati direttamente in Data Lake Storage Gen1 dal cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md): è possibile ricevere eventi da Hub eventi e scriverli in Data Lake Storage Gen1 usando [.NET SDK di Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dati relazionali
È inoltre possibile recuperare i dati dai database relazionali. I database relazionali raccolgono nel tempo elevate quantità di dati che possono fornire informazioni significative se elaborate tramite una pipeline Big Data. Per spostare tali dati in Data Lake Storage Gen1, è possibile usare gli strumenti seguenti.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati di log del server Web (caricamento tramite applicazioni personalizzate)
Questo tipo di set di dati è indicato in modo specifico perché l'analisi dei dati di log dei server Web è un caso d'uso comune per le applicazioni di Big Data e richiede il caricamento di grandi volumi di file di log in Data Lake Storage Gen1. Per scrivere script o applicazioni per il caricamento dei dati è possibile usare uno degli strumenti seguenti.

* [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK di Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)

Per il caricamento di dati di log del server Web e per il caricamento di altri tipi di dati, ad esempio i dati delle valutazioni dei social network, è consigliabile scrivere script o applicazioni personalizzate che offrono la flessibilità di includere il componente di caricamento dei dati come parte dell'applicazione Big Data più grande. In alcuni casi, questo codice può essere rappresentato da uno script o un'utilità della riga di comando. In altri casi, il codice può essere usato per integrare l'elaborazione di Big Data in un'applicazione o una soluzione aziendale.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati ai cluster Azure HDInsight
La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, Storm) supporta Data Lake Storage Gen1 come repository di archiviazione dei dati. I cluster HDInsight accedono ai dati dai BLOB di archiviazione di Azure (WASB). Per prestazioni ottimali, è possibile copiare i dati da WASB a un account Data Lake Storage Gen1 associato al cluster. Per copiare i dati è possibile usare gli strumenti seguenti.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servizio AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Data factory di Azure](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dati archiviati in locale o in cluster IaaS Hadoop
Grandi quantità di dati possono essere archiviati in cluster Hadoop esistenti, localmente, nei computer che usano HDFS. I cluster Hadoop potrebbero essere in una distribuzione locale o all'interno di un cluster IaaS in Azure. Potrebbe essere necessario copiare tali dati in Azure Data Lake Storage Gen1 una sola volta o regolarmente. Sono disponibili diverse opzioni che è possibile usare per ottenere questo. Di seguito è riportato un elenco di alternative con i relativi compromessi.

| Approccio | Dettagli | Vantaggi | Considerazioni |
| --- | --- | --- | --- |
| Usare Azure Data Factory (ADF) per copiare i dati direttamente dai cluster Hadoop in Azure Data Lake Storage Gen1 |[ADF supporta HDFS come origine dati](../data-factory/connector-hdfs.md) |ADF fornisce il supporto nativo per HDFS e il monitoraggio e gestione end-to-end di prima classe |Richiede che il gateway di gestione dati sia distribuito localmente nel cluster IaaS |
| Esportare dati da Hadoop come file. Copiare quindi i file in Azure Data Lake Storage Gen1 usando un meccanismo appropriato. |È possibile copiare i file in Azure Data Lake Storage Gen1 usando: <ul><li>[Azure PowerShell per sistema operativo Windows](data-lake-store-get-started-powershell.md)</li><li>[Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)</li><li>App personalizzata che usa qualsiasi SDK di Data Lake Storage Gen1</li></ul> |Guida introduttiva per iniziare. È possibile eseguire caricamenti personalizzati |Processo in più passaggi che prevede più tecnologie. La gestione e il monitoraggio miglioreranno fino a rappresentare una sfida nel corso del tempo data la natura personalizzata degli strumenti |
| Usare Distcp per copiare dati da Hadoop ad Archiviazione di Azure. Copiare quindi i dati da Archiviazione di Azure a Data Lake Storage Gen1 usando un meccanismo appropriato. |È possibile copiare i dati da Archiviazione di Azure a Data Lake Storage Gen1 usando: <ul><li>[Data factory di Azure](../data-factory/copy-activity-overview.md)</li><li>[strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache in esecuzione nei cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |È possibile usare strumenti open source. |Processo in più passaggi che prevede più tecnologie |

### <a name="really-large-datasets"></a>Set di dati di grandi dimensioni
L'uso dei metodi descritti in precedenza per il caricamento di set di dati di più terabyte può talvolta risultare lento e costoso. In questi casi, è possibile usare le opzioni seguenti.

* **Uso di Azure ExpressRoute**. Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Ciò offre un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su ExpressRoute](../expressroute/expressroute-introduction.md).
* **Caricamento "offline" dei dati**. Se l'uso di Azure ExpressRoute non è possibile per qualsiasi motivo, si può usare il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md) per inviare le unità disco rigido coi dati a un data center di Azure. I dati vengono caricati prima di tutto in BLOB di archiviazione di Azure. È quindi possibile usare [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) o lo [strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) per copiare i dati dai BLOB di archiviazione di Azure a Data Lake Storage Gen1.

  > [!NOTE]
  > Durante l'uso del servizio di Importazione/Esportazione, le dimensioni dei file nei dischi inviati al data center di Azure non devono superare i 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Elaborare i dati archiviati in Data Lake Storage Gen1
Quando i dati sono disponibili in Data Lake Storage Gen1, è possibile eseguire l'analisi sui dati tramite le applicazioni di Big Data supportate. Attualmente, è possibile usare Azure HDInsight e Azure Data Lake Analytics per eseguire processi di analisi dei dati sui dati archiviati in Data Lake Storage Gen1.

![Analizzare i dati in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analizzare i dati in Data Lake Storage Gen1")

Esaminare gli esempi seguenti.

* [Creare un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Scaricare i dati da Data Lake Storage Gen1
È anche possibile che si voglia scaricare o spostare i dati da Azure Data Lake Storage Gen1 per scenari simili ai seguenti:

* Spostare i dati in altri repository per l'interfaccia con le pipeline di elaborazione dati esistenti. È possibile, ad esempio, che sia necessario spostare i dati da Data Lake Storage Gen1 al database SQL di Azure o al server SQL locale.
* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Estrarre i dati da Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Estrarre i dati da Data Lake Storage Gen1")

In questi casi, è possibile usare le opzioni seguenti:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

È anche possibile usare i metodi seguenti per scrivere un'applicazione o uno script personalizzato per scaricare i dati da Data Lake Storage Gen1.

* [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK di Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualizzare i dati in Data Lake Storage Gen1
È possibile usare una combinazione di servizi per creare rappresentazioni visive dei dati archiviati in Data Lake Storage Gen1.

![Visualizzare i dati in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualizzare i dati in Data Lake Storage Gen1")

* Iniziare usando [Azure Data Factory per spostare i dati da Data Lake Storage Gen1 ad Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Successivamente è possibile [integrare Power BI con Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) per creare la rappresentazione visiva dei dati.
