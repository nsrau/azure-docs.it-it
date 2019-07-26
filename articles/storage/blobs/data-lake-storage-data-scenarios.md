---
title: Scenari di dati con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conoscere i diversi scenari e gli strumenti disponibili per inserire, elaborare, scaricare e visualizzare i dati in Data Lake Storage Gen2, noto in precedenza come Azure Data Lake Store
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.openlocfilehash: 010b7bc38caf83c12dd0d8b8e731fdbad6e45256
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422872"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data

L'elaborazione di Big Data prevede quattro fasi principali:

> [!div class="checklist"]
> * Inserimento di grandi quantità di dati in un archivio dati, in tempo reale o in batch
> * Elaborazione dei dati
> * Download dei dati
> * Visualizzazione dei dati

Iniziare creando un account di archiviazione e un file system. Quindi, concedere l'accesso ai dati. Le prime sezioni di questo articolo consentono di eseguire queste attività. Nelle sezioni rimanenti, verranno evidenziate le opzioni e gli strumenti per ogni fase di elaborazione.

## <a name="create-a-data-lake-storage-gen2-account"></a>Creare un account di Data Lake Storage Gen2

Un account Data Lake Storage Gen2 è un account di archiviazione con uno spazio dei nomi gerarchico. 

Per crearne uno, vedere [Avvio rapido: Creare un account di archiviazione di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Creare un file system

Un *file system* è un contenitore per file e cartelle. È necessario almeno uno di essi per iniziare l'inserimento di dati nell'account di archiviazione.  Ecco un elenco di strumenti che è possibile usare per crearli.

|Strumento | Materiale sussidiario |
|---|--|
|Esplora archivi Azure | [Creare un file system usando Storage Explorer](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|AzCopy | [Creare un contenitore BLOB o una condivisione file usando AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Interfaccia della riga di comando (CLI) Hadoop File System (HDFS) con HDInsight |[Creare un file system usando HDFS con HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Codice in un Notebook di Azure Databricks|[Creare il file system per l'account di archiviazione (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Creare e montare un file system (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

È più semplice creare i file System usando AzCopy o Storage Explorer. Richiede un po' più lavoro per creare i file system con HDInsight e Databricks. Tuttavia, se si prevede di usare comunque HDInsight o i cluster Databricks per l'elaborazione dei dati, allora si possono creare i cluster prima di tutto e usare la CLI di HDFS per creare i file system.  

## <a name="grant-access-to-the-data"></a>Concedere l'accesso ai dati

Impostare le autorizzazioni appropriate di accesso per l'account e i dati nell'account, prima di iniziare ad inserire i dati.

È possibile concedere l'accesso in tre modi:

* Assegnare uno di questi ruoli a un utente, gruppo, identità gestita dall'utente o entità servizio:

  [Lettore dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Proprietario dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Usare un token di firma di accesso condiviso (SAS).

* Usare una chiave dell'account di archiviazione.

Questa tabella illustra come concedere l'accesso per ogni strumento o servizio di Azure.

|Strumento | Concedere l'accesso | Materiale sussidiario |
|---|--|---|
|Storage Explorer| Assegnazione di ruolo a utenti e gruppi | [Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Assegnazione di ruolo a utenti e gruppi <br>**or**<br> Usare un token di firma di accesso condiviso| [Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Creare con facilità una firma di accesso condiviso per scaricare un file da Archiviazione di Azure – tramite Azure Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Assegnare un ruolo a un'identità gestita assegnata dall'utente | [Creazione di un cluster HDInsight con Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Data factory di Azure| Assegnare un ruolo a un'identità gestita assegnata dall'utente<br>**or**<br> Assegnare un ruolo a un'entità servizio<br>**or**<br> Usare una chiave dell'account di archiviazione | [Proprietà del servizio collegato](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|HDInsight di Azure| Assegnare un ruolo a un'identità gestita assegnata dall'utente | [Creazione di un cluster HDInsight con Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Assegnare un ruolo a un'entità servizio | [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Per concedere l'accesso a specifici file e cartelle, vedere questi articoli.

* [Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Elenchi di controllo di accesso per file e directory](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Per maggiori informazioni sull'impostazione di altri aspetti della sicurezza, vedere la [Guida alla sicurezza di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Inserire i dati

Questa sezione descrive le diverse origini dei dati e i diversi modi in cui i dati possono essere inseriti in un account Data Lake Storage Gen2.

![Inserire i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Inserire i dati in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dati ad hoc

Si tratta di set di dati di piccole dimensioni usati per la creazione del prototipo di un'applicazione Big Data. L'inserimento di dati ad hoc può essere eseguito in modi diversi, a seconda dell'origine dei dati. 

Ecco un elenco di strumenti che è possibile usare per inserire dati ad hoc.

| origine dati | Inserire usando |
| --- | --- |
| Computer locale |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)|
| BLOB di Archiviazione di Azure |[Data factory di Azure](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp in esecuzione nel cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dati di streaming

Si tratta dei dati che possono essere generati da origini diverse, ad esempio applicazioni, dispositivi, sensori, ecc. Questi dati possono essere inseriti in Data Lake Storage Gen2 con numerosi strumenti diversi. Questi strumenti in genere acquisiscono ed elaborano i dati dei singoli eventi in tempo reale e quindi scrivono gli eventi in batch in Data Lake Storage Gen2 perché possano essere elaborati ulteriormente.

Ecco un elenco di strumenti che è possibile usare per inserire dati trasmessi.

|Strumento | Materiale sussidiario |
|---|--|
|Azure HDInsight Storm | [Scrivere in Apache Hadoop HDFS da Apache Storm in HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dati relazionali

È inoltre possibile recuperare i dati dai database relazionali. I database relazionali raccolgono nel tempo elevate quantità di dati che possono fornire informazioni significative se elaborate tramite una pipeline Big Data. Per spostare tali dati in Data Lake Storage Gen2, è possibile usare gli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire dati relazionali.

|Strumento | Materiale sussidiario |
|---|--|
|Data factory di Azure | [Attività Copy in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati di log del server Web (caricamento tramite applicazioni personalizzate)

Questo tipo di set di dati è indicato in modo specifico perché l'analisi dei dati di log dei server Web è un caso d'uso comune per le applicazioni di Big Data e richiede il caricamento di grandi volumi di file di log in Data Lake Storage Gen2. Per scrivere script o applicazioni per il caricamento dei dati è possibile usare uno degli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire dati log del server Web.

|Strumento | Materiale sussidiario |
|---|--|
|Data factory di Azure | [Attività Copy in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Per il caricamento di dati di log del server Web e per il caricamento di altri tipi di dati, ad esempio i dati delle valutazioni dei social network, è consigliabile scrivere script o applicazioni personalizzate che offrono la flessibilità di includere il componente di caricamento dei dati come parte dell'applicazione Big Data più grande. In alcuni casi, questo codice può essere rappresentato da uno script o un'utilità della riga di comando. In altri casi, il codice può essere usato per integrare l'elaborazione di Big Data in un'applicazione o una soluzione aziendale.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati ai cluster Azure HDInsight

La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, Storm) supporta Data Lake Storage Gen2 come repository di archiviazione dei dati. I cluster HDInsight accedono ai dati dai BLOB di archiviazione di Azure (WASB). Per prestazioni ottimali, è possibile copiare i dati da WASB a un account Data Lake Storage Gen2 associato al cluster. Per copiare i dati è possibile usare gli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire i dati associati ai cluster HDInsight.

|Strumento | Materiale sussidiario |
|---|--|
|Apache DistCp | [Usare DistCp per copiare dati tra i BLOB del servizio di Archiviazione di Microsoft Azure e i Data Lake Storage Gen2 di Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Strumento AzCopy | [Trasferire dati con AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Data factory di Azure | [Copiare dati da e verso Azure Data Lake Storage Gen2 tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dati archiviati in locale o in cluster IaaS Hadoop

Grandi quantità di dati possono essere archiviati in cluster Hadoop esistenti, localmente, nei computer che usano HDFS. I cluster Hadoop potrebbero essere in una distribuzione locale o all'interno di un cluster IaaS in Azure. Potrebbe essere necessario copiare tali dati in Azure Data Lake Storage Gen2 una sola volta o regolarmente. Sono disponibili diverse opzioni che è possibile usare per ottenere questo. Di seguito è riportato un elenco di alternative con i relativi compromessi.

| Approccio | Dettagli | Vantaggi | Considerazioni |
| --- | --- | --- | --- |
| Usare Azure Data Factory (ADF) per copiare i dati direttamente dai cluster Hadoop in Azure Data Lake Storage Gen2 |[ADF supporta HDFS come origine dati](../../data-factory/connector-hdfs.md) |ADF fornisce il supporto nativo per HDFS e il monitoraggio e gestione end-to-end di prima classe |Richiede che il gateway di gestione dati sia distribuito localmente nel cluster IaaS |
| Usare Distcp per copiare dati da Hadoop ad Archiviazione di Azure. Copiare quindi i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando un meccanismo appropriato. |È possibile copiare i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando: <ul><li>[Data factory di Azure](../../data-factory/copy-activity-overview.md)</li><li>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp Apache in esecuzione nei cluster HDInsight](data-lake-storage-use-distcp.md)</li></ul> |È possibile usare strumenti open source. |Processo in più passaggi che prevede più tecnologie |

### <a name="really-large-datasets"></a>Set di dati di grandi dimensioni

L'uso dei metodi descritti in precedenza per il caricamento di set di dati di più terabyte può talvolta risultare lento e costoso. In questi casi, è possibile utilizzare Azure ExpressRoute.  

Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Ciò offre un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Elaborare i dati

Quando i dati sono disponibili in Data Lake Storage Gen2, è possibile eseguire l'analisi sui dati tramite le applicazioni di Big Data supportate. 

![Analizzare i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizzare i dati in Data Lake Storage Gen2")

Ecco un elenco di strumenti che è possibile usare per eseguire processi di analisi dei dati sui dati archiviati in Data Lake Storage Gen2.

|Strumento | Materiale sussidiario |
|---|--|
|HDInsight di Azure | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Avvio rapido: Analizzare i dati in Azure Data Lake Storage Gen2 con Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Esercitazione: Estrarre, trasformare e caricare dati con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualizzare i dati

È possibile usare una combinazione di servizi per creare rappresentazioni visive dei dati archiviati in Data Lake Storage Gen2.

![Visualizzare i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualizzare i dati in Data Lake Storage Gen2")

* Iniziare usando [Azure Data Factory per spostare i dati da Data Lake Storage Gen2 ad Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Successivamente è possibile [integrare Power BI con Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) per creare la rappresentazione visiva dei dati.

## <a name="download-the-data"></a>Scaricare i dati

È anche possibile che si voglia scaricare o spostare i dati da Azure Data Lake Storage Gen2 per scenari simili ai seguenti:

* Spostare i dati in altri repository per l'interfaccia con le pipeline di elaborazione dati esistenti. È possibile, ad esempio, che sia necessario spostare i dati da Data Lake Storage Gen2 al database SQL di Azure o al server SQL locale.

* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Estrarre i dati da Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Estrarre i dati da Data Lake Storage Gen2")

Ecco un elenco di strumenti che è possibile usare per scaricare dati da Data Lake Storage Gen2.

|Strumento | Materiale sussidiario |
|---|--|
|Data factory di Azure | [Attività Copy in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Usare DistCp per copiare dati tra i BLOB del servizio di Archiviazione di Microsoft Azure e i Data Lake Storage Gen2 di Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
