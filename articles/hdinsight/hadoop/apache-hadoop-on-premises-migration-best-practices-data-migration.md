---
title: Eseguire la migrazione di cluster di Apache Hadoop locali ad Azure HDInsight-migrazione dei dati
description: Informazioni sulle procedure consigliate per la migrazione dei dati di cluster Hadoop locali ad Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 567edca422237c71f0d69c862a17fbc0d2a72795
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735920"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Procedure consigliate per la migrazione dei dati

Questo articolo offre indicazioni per la migrazione dei dati ad Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Eseguire la migrazione di dati locali ad Azure

Esistono due opzioni principali per eseguire la migrazione dei dati da un sistema locale all'ambiente Azure:

1.  Trasferimento dei dati sulla rete con TLS
    1. Internet: è possibile trasferire dati nell'archiviazione di Azure tramite una connessione Internet normale usando uno dei diversi strumenti come: Azure Storage Explorer, AzCopy, Azure Powershell e l'interfaccia della riga di comando di Azure.  Per altre informazioni, vedere [Spostamento dei dati da e verso Archiviazione di Azure](../../storage/common/storage-moving-data.md).
    2. ExpressRoute: ExpressRoute è un servizio di Azure che permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in una struttura con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Per altre informazioni, vedere [Creare e modificare un circuito ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Trasferimento dei dati online Data Box: Data Box Edge e Data Box Gateway sono prodotti per il trasferimento dei dati online che fungono da gateway di archiviazione di rete per gestire i dati tra il sito e Azure. Data Box Edge, un dispositivo di rete locale, trasferisce i dati verso e da Azure e usa risorse di calcolo perimetrali abilitate per intelligenza artificiale per elaborare i dati. Data Box Gateway è un'appliance virtuale con funzionalità di gateway di archiviazione. Per altre informazioni, vedere [Documentazione di Azure Data Box - Trasferimento online](https://docs.microsoft.com/azure/databox-online/).
1.  Spedizione dei dati offline
    1. Trasferimento dei dati offline Data Box: i dispositivi Data Box, Data Box Disk e Data Box Heavy consentono di trasferire quantità elevate di dati in Azure quando non è possibile usare la rete. Questi dispositivi per il trasferimento dei dati offline vengono spediti tra l'organizzazione e il data center di Azure. Usano la crittografia AES per la protezione dei dati in transito e vengono sottoposti a un processo completo di bonifica dopo il caricamento per l'eliminazione dei dati. Per ulteriori informazioni sui dispositivi Data Box Transfer offline, vedere [Azure Data Box Documentation-offline Transfer](https://docs.microsoft.com/azure/databox/). Per altre informazioni sulla migrazione dei cluster Hadoop, vedere [usare Azure Data Box per eseguire la migrazione da un archivio HDFS locale ad archiviazione di Azure](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

La tabella seguente indica una durata di trasferimento dei dati approssimativa, basata sul volume dei dati e sulla larghezza di banda della rete. Usare un'appliance Data Box se si prevede che la migrazione dei dati richieda più di tre settimane.

|**Qtà di dati**|**Larghezza di banda della rete**||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 giorni|1 giorno| 2 ore|14 minuti|
|10 TB|22 giorni|10 giorni|1 giorno|2 ore|
|35 TB|76 giorni|34 giorni|3 giorni|8 ore|
|80 TB|173 giorni|78 giorni|8 giorni|19 ore|
|100 TB|216 giorni|97 giorni|10 giorni|1 giorno|
|200 TB|1 anno|194 giorni|19 giorni|2 giorni|
|500 TB|3 anni|1 anno|49 giorni|5 giorni|
|1 PB|6 anni|3 anni|97 giorni|10 giorni|
|2 PB|12 anni|5 anni|194 giorni|19 giorni|

Gli strumenti nativi di Azure, ad esempio Apache Hadoop, DistCp, Azure Data Factory e AzureCp, possono essere usati per trasferire i dati in rete. Per lo stesso scopo può essere usato anche lo strumento di terze parti WANDisco. Per i trasferimenti continui di dati da un sistema locale ai sistemi di archiviazione di Azure è possibile usare Apache Kafka Mirrormaker e Apache Sqoop.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Considerazioni sulle prestazioni con Apache Hadoop DistCp


DistCp è un progetto Apache che usa un processo di mapping di MapReduce per trasferire i dati, gestire gli errori ed eseguire il ripristino. Assegna un elenco di file di origine a ogni attività di mapping. L'attività di mapping copia quindi tutti i file ricevuti in assegnazione nella destinazione. Esistono diverse tecniche per migliorare le prestazioni di DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentare il numero di mapper

DistCp prova a creare attività di mapping in modo che ognuna possa copiare all'incirca lo stesso numero di byte. Per impostazione predefinita, i processi di DistCp usano 20 mapper. L'uso di più mapper per Distcp (con il parametro m alla riga di comando) aumenta il parallelismo durante il processo di trasferimento dei dati e consente di ridurre la durata del trasferimento. Tuttavia, esistono due aspetti da considerare quando si aumenta il numero di mapper:

1. La granularità minima di DistCp è rappresentata da un singolo file. Specificare un numero di mapper maggiore rispetto al numero di file di origine non risulta efficace e determina una perdita in termini di risorse cluster disponibili.
1. Prendere in considerazione la memoria YARN disponibile nel cluster per determinare il numero di mapper. Ogni attività di mapping viene avviata come contenitore YARN. Supponendo che nel cluster non siano in esecuzione altri carichi di lavoro consistenti, il numero di mapper può essere determinato dalla formula seguente: m = (numero di nodi del ruolo di lavoro \* memoria YARN per ogni nodo del ruolo di lavoro) / dimensione del contenitore YARN. Tuttavia, se la memoria è usata da altre applicazioni, scegliere di usare solo una parte della memoria YARN per i processi di DistCp.

### <a name="use-more-than-one-distcp-job"></a>Usare più processi di DistCp

Quando la dimensione del set di dati da spostare supera 1 TB, usare più processi di DistCp. Usando più processi l'impatto degli errori risulta limitato. Se un processo non riesce, è sufficiente riavviare tale processo anziché tutti i processi.

### <a name="consider-splitting-files"></a>Considerare la possibilità di dividere i file

Se è presente un numero ridotto di file di grandi dimensioni, è possibile dividere i file in blocchi da 256 MB per ottenere una maggiore concorrenza potenziale con più mapper.

### <a name="use-the-strategy-command-line-parameter"></a>Usare il parametro strategy della riga di comando

È consigliabile usare il parametro `strategy = dynamic` nella riga di comando. Il valore predefinito del parametro `strategy` è `uniform size`, nel qual caso ogni mapping copia all'incirca lo stesso numero di byte. Se questo parametro viene impostato su `dynamic`, il file listato viene diviso in diversi blocchi. Il numero di blocchi è un multiplo del numero di mapping. A ogni attività di mapping viene assegnato uno dei blocchi. Al termine dell'elaborazione di tutti i percorsi di un blocco, il blocco corrente viene eliminato e viene acquisito un nuovo blocco. Il processo continua fino a quando non sono disponibili altri blocchi. Questo approccio dinamico consente alle attività di mapping più veloci di utilizzare un numero maggiore di percorsi rispetto alle attività più lente, determinando una velocizzazione complessiva del processo di DistCp.

### <a name="increase-the-number-of-threads"></a>Aumentare il numero di thread

Verificare se aumentando il valore del parametro `-numListstatusThreads` migliorano le prestazioni. Questo parametro controlla il numero di thread da usare per la creazione dell'elenco di file e 40 è il valore massimo.

### <a name="use-the-output-committer-algorithm"></a>Usare l'algoritmo committer dell'output

Verificare se passando il parametro `-Dmapreduce.fileoutputcommitter.algorithm.version=2` migliorano le prestazioni di DistCp. Questo algoritmo committer dell'output presenta ottimizzazioni per la scrittura di file di output nella destinazione. Il comando seguente è un esempio che illustra l'utilizzo di parametri diversi:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrazione di metadati

### <a name="apache-hive"></a>Apache Hive

È possibile eseguire la migrazione del metastore Hive usando gli script o la replica di database.

#### <a name="hive-metastore-migration-using-scripts"></a>Migrazione del metastore Hive con gli script

1. Generare il DDL hive da locale metastore Hive. Questo passaggio può essere eseguito tramite uno [script Bash wrapper](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Modificare l'istruzione DDL generata per sostituire l'URL Hadoop Distributed File System con gli URL WASB/ADLS/ABFS.
1. Eseguire l'istruzione DDL aggiornata sul metastore dal cluster HDInsight.
1. Assicurarsi che la versione del metastore Hive sia compatibile per il passaggio da sistemi locali a cloud.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migrazione del metastore Hive con la replica di database

- Configurare la replica tra il database del metastore Hive locale e il database del metastore HDInsight.
- Usare Hive MetaTool per sostituire l'URL HDFS con gli URL WASB/ADLS/ABFS, ad esempio:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Esportare i criteri locali di Ranger nei file xml.
- Trasformare i percorsi basati su HDFS specifici in locale in WASB/ADLS usando uno strumento come XSLT.
- Importare i criteri in Ranger che viene eseguito in HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo successivo di questa serie:

- [Security and DevOps best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-security-devops.md) (Procedure consigliate relative alla sicurezza e a DevOps per la migrazione da un sistema locale ad Azure HDInsight Hadoop)
