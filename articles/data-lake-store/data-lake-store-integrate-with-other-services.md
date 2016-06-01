<properties
   pageTitle="Integrazione di Data Lake Store con altri servizi di Azure | Azure"
   description="Informazioni sulle modalità di integrazione di Data Lake Store con altri servizi di Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/17/2016"
   ms.author="nitinme"/>

# Integrazione di Data Lake Store con altri servizi di Azure

Azure Data Lake Store può essere usato in combinazione con altri servizi di Azure per abilitare il supporto di una gamma più ampia di scenari. L'articolo seguente elenca i servizi con cui Data Lake Store può essere integrato.

## Usare Archivio Data Lake con Azure HDInsight

È possibile eseguire il provisioning di un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa Data Lake Store come archivio conforme a HDFS. Per questa versione, per i cluster Hadoop e Storm in Windows e Linux è possibile usare Data Lake Store solo come un'ulteriore risorsa di archiviazione. Tali cluster continuano a usare l'archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Tuttavia, per i cluster HBase in Windows e Linux è possibile usare Data Lake Store come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva oppure in entrambe le modalità.

Per istruzioni su come eseguire il provisioning di un cluster HDInsight con Data Lake Store, vedere:

* [Effettuare il provisioning di un cluster HDInsight con Archivio Data Lake tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Eseguire il provisioning di un cluster HDInsight con Data Lake Store mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Se si preferiscono i video** Usare i collegamenti seguenti per accedere a video con le istruzioni su come usare i cluster Archivio Data Lake con HDInsight.

* [Creare un cluster HDInsight con accesso ad Archivio Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Una volta configurato il cluster, [accedere ai dati in Archivio Data Lake usando gli script Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)


## Usare Archivio Data Lake con Analisi Data Lake di Azure

[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) consente di usare i Big Data a livello di cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte e addirittura exabyte di dati che possono essere archiviati nelle numerose origini dati supportate, ad esempio Data Lake Store. Data Lake Analytics è ottimizzato in modo specifico per usare Azure Data Lake Store in modo da fornire il più elevato livello di prestazioni, velocità effettiva e parallelizzazione dei carichi di lavoro dei Big Data.

Per istruzioni su come usare Data Lake Analytics con Data Lake Store, vedere [Introduzione a Data Lake Analytics mediante Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Se si preferiscono i video** Usare i collegamenti seguenti per accedere a video con le istruzioni su come usare i cluster Archivio Data Lake con HDInsight.

* [Connettere Analisi Azure Data Lake ad Archivio Azure Data Lake](https://mix.office.com/watch/qwji0dc9rx9k)
* [Accedere ad Archivio Azure Data Lake con Analisi Azure Data Lake](https://mix.office.com/watch/1n0s45up381a8)


## Usare Archivio Data Lake con Data factory di Azure

È possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per inserire dati da tabelle di Azure, database SQL di Azure, data warehouse SQL di Azure, BLOB di archiviazione di Azure e database locali. Essendo un elemento di primo piano nell'ecosistema Azure, Data factory di Azure può essere usato per gestire l'inserimento di dati da queste origini nel servizio Data Lake Store di Azure.

Per istruzioni su come usare Data factory di Azure con Data Lake Store, vedere [Spostare dati da e verso Data Lake Store mediante Data factory](../data-factory/data-factory-azure-datalake-connector.md).

**Ancora video!** Vedere il video sull'[orchestrazione dei dati con Azure Data Factory per Archivio Azure Data Lake](https://mix.office.com/watch/1oa7le7t2u4ka).

## Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake

Archivio Data Lake di Azure fornisce uno strumento da riga di comando, AdlCopy, che consente di copiare dati da Archiviazione BLOB di Azure a un account di Archivio Data Lake. Per altre informazioni, vedere [Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## Copiare i dati dal database SQL di Azure nell'Archivio Data Lake

È possibile usare Apache Sqoop per importare ed esportare dati da un database SQL di Azure in un Archivio Data Lake. Per altre informazioni, vedere [Copiare i dati tra Archivio Data Lake e un database SQL di Azure usando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Guardare questo video** su come [usare Apache Sqoop per spostare i dati tra origini relazionali e Archivio Azure Data Lake](https://mix.office.com/watch/1butcdjxmu114).

## Uso di Archivio Data Lake con l'analisi di flusso

È possibile usare Archivio Data Lake come uno degli output per archiviare dati di flusso usando l'analisi di flusso di Azure. Per altre informazioni, vedere [Trasmettere i dati dal BLOB di archiviazione di Azure ad Archivio Data Lake usando l'analisi di flusso di Azure](data-lake-store-stream-analytics.md).

## Uso di Archivio Data Lake con Power BI

È possibile usare Power BI per importare dati da un account Archivio Data Lake per analizzare e visualizzare i dati stessi. Per altre informazioni, vedere [Analisi dei dati in Archivio Data Lake con Power BI](data-lake-store-power-bi.md).

## Uso di Archivio Data Lake con Catalogo dati di Azure

È possibile registrare dati da Archivio Data Lake nel Catalogo dati di Azure per rendere i dati individuabili nell'intera organizzazione. Per altre informazioni, vedere [Registrazione i dati da Archivio Data Lake in Catalogo dati di Azure](data-lake-store-with-data-catalog.md).


## Vedere anche

- [Panoramica di Data Lake Store di Azure](data-lake-store-overview.md)
- [Introduzione a Data Lake Store mediante il portale](data-lake-store-get-started-portal.md)
- [Introduzione a Data Lake Store mediante PowerShell](data-lake-store-get-started-powershell.md)  

<!---HONumber=AcomDC_0518_2016-->