---
title: Scenari di dati con Data Lake Store | Microsoft Docs
description: "Scoprire i diversi scenari e gli strumenti che è possibile usare per inserire, elaborare, scaricare e visualizzare i dati in un Archivio Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6428c6d9fcb577f18221ee48a61456c460bd8176
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Uso di Archivio Data Lake di Azure per requisiti Big Data
L'elaborazione di Big Data prevede quattro fasi principali:

* Inserimento di grandi quantità di dati in un archivio dati, in tempo reale o in batch
* Elaborazione dei dati
* Download dei dati
* Visualizzazione dei dati

In questo articolo sono descritte le quattro fasi per Archivio Azure Data Lake per illustrare le opzioni e gli strumenti disponibili per soddisfare le esigenze di Big Data.

## <a name="ingest-data-into-data-lake-store"></a>Inserire i dati in Archivio Data Lake
Questa sezione descrive le diverse origini dei dati e i diversi modi in cui i dati possono essere inseriti in un account di Archivio Data Lake.

![Inserire i dati in Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "Inserire i dati in Data Lake Store")

### <a name="ad-hoc-data"></a>Dati ad hoc
Si tratta di set di dati di piccole dimensioni usati per la creazione del prototipo di un'applicazione Big Data. L'inserimento di dati ad hoc può essere eseguito in modi diversi, a seconda dell'origine dei dati.

| Origine dati | Inserire usando |
| --- | --- |
| Computer locale |<ul> <li>[Portale di Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interfaccia della riga di comando multipiattaforma di Azure 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usare Strumenti di Data Lake per Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| BLOB di Archiviazione di Azure |<ul> <li>[Data factory di Azure](../data-factory/connector-azure-data-lake-store.md)</li> <li>[strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp in esecuzione nel cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dati di streaming
Si tratta dei dati che possono essere generati da origini diverse, ad esempio applicazioni, dispositivi, sensori, ecc. Questi dati possono essere inseriti in un Archivio Data Lake tramite strumenti diversi. Questi strumenti in genere acquisiscono ed elaborano i dati di un singolo evento in tempo reale e quindi scrivono gli eventi in batch in Archivio Data Lake in modo che possono essere elaborati ulteriormente.

Di seguito sono elencati gli strumenti che è possibile usare:

* [Analisi di flusso di Azure](../stream-analytics/stream-analytics-data-lake-output.md): gli eventi inseriti in Hub eventi possono essere scritti in Azure Data Lake usando un output di Azure Data Lake Store.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) : è possibile scrivere i dati direttamente in Data Lake Store dal cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md): è possibile ricevere eventi da Hub eventi e scriverli in Data Lake Store usando [.NET SDK di Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dati relazionali
È inoltre possibile recuperare i dati dai database relazionali. I database relazionali raccolgono nel tempo elevate quantità di dati che possono fornire informazioni significative se elaborate tramite una pipeline Big Data. Per spostare i dati in Archivio Data Lake è possibile usare gli strumenti seguenti.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati di log del server Web (caricamento tramite applicazioni personalizzate)
Questo tipo di set di dati è indicato in modo specifico perché l'analisi dei dati di log del server Web è un caso d'uso comune per le applicazioni Big Data e richiede il caricamento di grandi volumi di file di log in Archivio Data Lake. Per scrivere script o applicazioni per il caricamento dei dati è possibile usare uno degli strumenti seguenti.

* [Interfaccia della riga di comando multipiattaforma di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK di Archivio Azure Data Lake](data-lake-store-get-started-net-sdk.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)

Per il caricamento di dati di log del server Web e per il caricamento di altri tipi di dati, ad esempio i dati delle valutazioni dei social network, è consigliabile scrivere script o applicazioni personalizzate che offrono la flessibilità di includere il componente di caricamento dei dati come parte dell'applicazione Big Data più grande. In alcuni casi, questo codice può essere rappresentato da uno script o un'utilità della riga di comando. In altri casi, il codice può essere usato per integrare l'elaborazione di Big Data in un'applicazione o una soluzione aziendale.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati ai cluster Azure HDInsight
La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, Storm) supportano Archivio Data Lake come repository di archiviazione dei dati. I cluster HDInsight accedono ai dati dai BLOB di archiviazione di Azure (WASB). Per prestazioni ottimali, è possibile copiare i dati dal WASB in un account Archivio Data Lake associato al cluster. Per copiare i dati è possibile usare gli strumenti seguenti.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servizio AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Data factory di Azure](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dati archiviati in locale o in cluster IaaS Hadoop
Grandi quantità di dati possono essere archiviati in cluster Hadoop esistenti, localmente, nei computer che usano HDFS. I cluster Hadoop potrebbero essere in una distribuzione locale o all'interno di un cluster IaaS in Azure. Potrebbe essere necessario copiare tali dati in un Archivio Azure Data Lake una sola volta o regolarmente. Sono disponibili diverse opzioni che è possibile usare per ottenere questo. Di seguito è riportato un elenco di alternative con i relativi compromessi.

| Approccio | Dettagli | Vantaggi | Considerazioni |
| --- | --- | --- | --- |
| Usare Azure Data Factory (ADF) per copiare i dati direttamente dal cluster Hadoop nell'Archivio Data Lake di Azure |[ADF supporta HDFS come origine dati](../data-factory/connector-hdfs.md) |ADF fornisce il supporto nativo per HDFS e il monitoraggio e gestione end-to-end di prima classe |Richiede che il gateway di gestione dati sia distribuito localmente nel cluster IaaS |
| Esportare dati da Hadoop come file. Quindi copiare i file in Archivio Data Lake di Azure mediante la procedura appropriata. |È possibile copiare i file in Azure Data Lake Store usando:  <ul><li>[Azure PowerShell per sistema operativo Windows](data-lake-store-get-started-powershell.md)</li><li>[Interfaccia della riga di comando multipiattaforma di Azure 2.0 per sistemi operativi non Windows](data-lake-store-get-started-cli-2.0.md)</li><li>Applicazione personalizzata che usa qualsiasi SDK di Data Lake Store</li></ul> |Guida introduttiva per iniziare. È possibile eseguire caricamenti personalizzati |Processo in più passaggi che prevede più tecnologie. La gestione e il monitoraggio miglioreranno fino a rappresentare una sfida nel corso del tempo data la natura personalizzata degli strumenti |
| Usare Distcp per copiare dati da Hadoop ad Archiviazione di Azure. Quindi copiare i file da Archiviazione di Azure mediante la procedura appropriata. |È possibile copiare i dati da Archiviazione di Azure in Data Lake Store usando:  <ul><li>[Data factory di Azure](../data-factory/copy-activity-overview.md)</li><li>[strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache in esecuzione nei cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |È possibile usare strumenti open source. |Processo in più passaggi che prevede più tecnologie |

### <a name="really-large-datasets"></a>Set di dati di grandi dimensioni
L'uso dei metodi descritti in precedenza per il caricamento di set di dati di più terabyte può talvolta risultare lento e costoso. In questi casi, è possibile usare le opzioni seguenti.

* **Uso di Azure ExpressRoute**. Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Ciò offre un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su ExpressRoute](../expressroute/expressroute-introduction.md).
* **Caricamento "offline" dei dati**. Se l'uso di Azure ExpressRoute non è possibile per qualsiasi motivo, si può usare il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md) per inviare le unità disco rigido coi dati a un data center di Azure. I dati vengono caricati prima di tutto in BLOB di archiviazione di Azure. È quindi possibile usare [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) o lo [strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) per copiare i dati dai BLOB di archiviazione di Azure a Data Lake Store.

  > [!NOTE]
  > Durante l'uso del servizio di Importazione/Esportazione, le dimensioni dei file nei dischi inviati al data center di Azure non devono superare i 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Elaborare i dati archiviati in Archivio Data Lake
Quando i dati sono disponibili in Archivio Data Lake è possibile eseguire l'analisi sui dati tramite le applicazioni Big Data supportate. Attualmente, è possibile usare Azure HDInsight e Analisi Data Lake per eseguire processi di analisi dei dati sui dati archiviati in Archivio Data Lake.

![Analizzare i dati in Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "Analizzare i dati in Data Lake Store")

Esaminare gli esempi seguenti.

* [Creare un cluster HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Scaricare i dati da Archivio Data Lake
È anche possibile che si desideri scaricare o spostare i dati da Archivio Azure Data Lake per scenari simili ai seguenti:

* Spostare i dati in altri repository per l'interfaccia con le pipeline di elaborazione dati esistenti. È possibile ad esempio che si desideri spostare i dati da Archivio Data Lake al database SQL di Azure o al server SQL locale.
* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Estrarre i dati da Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "Estrarre i dati da Data Lake Store")

In questi casi, è possibile usare le opzioni seguenti:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Data factory di Azure](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

È anche possibile usare i metodi seguenti per scrivere script o applicazioni personalizzate per scaricare i dati da Archivio Data Lake.

* [Interfaccia della riga di comando multipiattaforma di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK di Archivio Azure Data Lake](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizzare i dati in Archivio Data Lake
È possibile usare più servizi per creare rappresentazioni visive dei dati archiviati in Archivio Data Lake.

![Visualizzare i dati in Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "Visualizzare i dati in Data Lake Store")

* Iniziare usando [Azure Data Factory per spostare i dati da Data Lake Store ad Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Successivamente è possibile [integrare Power BI con Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) per creare la rappresentazione visiva dei dati.
