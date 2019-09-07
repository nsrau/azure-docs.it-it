---
title: Eseguire la migrazione di cluster di Apache Hadoop locali ad Azure HDInsight-architettura
description: Informazioni sulle procedure consigliate per l'architettura relative alla migrazione di cluster Hadoop locali ad Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: 4243100d74515576463a6812e31625ddc0ca1f48
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735891"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Procedure consigliate per l'architettura

Questo articolo include raccomandazioni per l'architettura dei sistemi Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Usare più cluster ottimizzati per i carichi di lavoro

Molte distribuzioni di Apache Hadoop in locale sono costituite da un singolo cluster di grandi dimensioni che supporta molti carichi di lavoro. Questo singolo cluster può essere complesso e potrebbero essere necessari compromessi per i singoli servizi per garantire la compatibilità di tutti gli elementi. Per la migrazione di cluster Hadoop locali ad Azure HDInsight occorre cambiare l'approccio.

I cluster Azure HDInsight sono progettati per un tipo specifico di utilizzo delle risorse di calcolo. Dato che l'archiviazione può essere condivisa tra più cluster, è possibile creare più cluster di calcolo ottimizzati per i carichi di lavoro per soddisfare le esigenze dei diversi processi. Ogni tipo di cluster ha la configurazione ottimale per un carico di lavoro specifico. Nella tabella seguente sono elencati i tipi di cluster supportati in HDInsight e i carichi di lavoro corrispondenti.

|**Carico di lavoro**|**Tipo di cluster HDInsight**|
|---|---|
|Elaborazione batch (ETL / ELT)|Hadoop, Spark|
|Data warehousing|Hadoop, Spark, Interactive Query|
|IoT / Streaming|Kafka, Storm, Spark|
|Elaborazione transazionale NoSQL|hbase|
|Query interattive e più veloci con la memorizzazione nella cache in memoria|Interactive Query|
|Data science|ML Services, Spark|

La tabella seguente illustra i diversi metodi che possono essere usati per creare un cluster HDInsight.

|**Strumento**|**Basato su browser**|**Riga di comando**|**API REST**|**SDK**|
|---|---|---|---|---|
|[Portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|x||||
|[Data factory di Azure](../hdinsight-hadoop-create-linux-clusters-adf.md)|x|X|X|X|
|[Interfaccia della riga di comando di Azure versione 1.0](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[SDK per Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Modelli di Gestione risorse di Azure](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Per altre informazioni, vedere l'articolo [Tipi di cluster in HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Usare cluster temporanei on demand

I cluster HDInsight potrebbero rimanere inutilizzati per lunghi periodi di tempo. Al fine di risparmiare sui costi delle risorse, HDInsight supporta cluster temporanei on demand, che possono essere eliminati dopo il corretto completamento del carico di lavoro.

Quando si elimina un cluster, l'account di archiviazione associato e i metadati esterni non vengono rimossi. Il cluster può successivamente essere ricreato usando gli stessi account di archiviazione e metastore.

Azure Data Factory può essere usato per pianificare la creazione di cluster HDInsight on demand. Per altre informazioni, vedere l'articolo [Creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Separare l'archiviazione dal calcolo

Le distribuzioni di Hadoop locali tipiche usano lo stesso set di computer per l'archiviazione dei dati e l'elaborazione dei dati. Condividendo la stessa posizione, le risorse di calcolo e archiviazione devono essere ridimensionate insieme.

Nei cluster HDInsight, le risorse di archiviazione e di calcolo non devono condividere la stessa posizione e possono essere in Archiviazione di Azure, Azure Data Lake Storage o in entrambi. La separazione dell'archiviazione dal calcolo offre i vantaggi seguenti:

- Condivisione dei dati tra i cluster.
- Uso di cluster temporanei dato che i dati non sono dipendenti dal cluster.
- Riduzione dei costi di archiviazione.
- Ridimensionamento separato delle risorse di archiviazione e calcolo.
- Replica dei dati tra aree.

I cluster di calcolo vengono creati in prossimità delle risorse dell'account di archiviazione in un'area di Azure per attenuare l'impatto sulle prestazioni della separazione di calcolo e archiviazione. Le reti ad alta velocità rendono efficiente l'accesso ai dati in archiviazione di Azure per i nodi di calcolo.

## <a name="use-external-metadata-stores"></a>Usare archivi di metadati esterni


Esistono due principali metastore che supportano i cluster HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). Il metastore Hive è il repository di schemi centrale che può essere usato dai motori di elaborazione dei dati, tra cui Hadoop, Spark, LLAP, Presto e Apache Pig. Il metastore Oozie archivia i dettagli sulla pianificazione e lo stato dei processi Hadoop in corso e completati.


HDInsight usa il database SQL di Azure per i metastore Hive e Oozie. Esistono due modi per configurare un metastore nei cluster HDInsight:

1. Metastore predefinito

    - Non sono previsti costi aggiuntivi.
    - Il metastore viene eliminato quando viene eliminato il cluster.
    - Il metastore non può essere condiviso tra cluster diversi.
    - Usa il database SQL di Azure di base, che prevede un limite di cinque DTU.

1. Metastore esterno personalizzato

    - Specificare un database SQL di Azure esterno come metastore.
    - I cluster possono essere creati ed eliminati senza perdere i metadati, inclusi i dettagli dei processi Hive e Oozie.
    - Un singolo database di metastore può essere condiviso con tipi diversi di cluster.
    - Il metastore può essere potenziato in base alle esigenze.
    - Per altre informazioni, vedere [Use external metadata stores in Azure HDInsight (Usare archivi di metadati esterni in Azure HDInsight)](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Procedure consigliate per il metastore Hive

Di seguito sono indicate alcune procedure consigliate per il metastore Hive di HDInsight:

- Usare un metastore personalizzato esterno per separare le risorse di calcolo e i metadati.
- Iniziare con un'istanza di SQL di Azure di livello S2, che offre 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.
- Non condividere il metastore creato per una versione del cluster HDInsight con i cluster di una versione diversa. Versioni diverse di Hive usano schemi diversi. Ad esempio, un metastore non può essere condiviso con cluster Hive 1.2 e Hive 2.1.
- Eseguire periodicamente il backup del metastore personalizzato.
- Mantenere il metastore e il cluster HDInsight nella stessa area.
- Monitora il Metastore per le prestazioni e la disponibilità usando gli strumenti di monitoraggio del database SQL di Azure, ad esempio portale di Azure o i log di monitoraggio di Azure.
- Eseguire il comando **ANALYZE TABLE** all'occorrenza per generare statistiche per tabelle e colonne. Ad esempio `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Procedure consigliate per diversi carichi di lavoro

- È consigliabile usare cluster LLAP per le query Hive interattive con tempo di risposta migliorato. [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) è una nuova funzionalità di Hive 2.0 che consente la memorizzazione nella cache in memoria delle query. LLAP rende molto più veloci le query Hive, in alcuni casi fino a  [26 volte più veloci rispetto a Hive 1.x](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Prendere in considerazione l'uso di processi Spark al posto dei processi Hive.
- È consigliabile sostituire le query basate su impala con query LLAP.
- È consigliabile sostituire processi MapReduce con processi Spark.
- È consigliabile sostituire processi batch di Spark a bassa latenza usando processi di Spark Structured Streaming.
- Prendere in considerazione l'uso di Azure Data Factory (ADF) 2.0 per l'orchestrazione dei dati.
- Prendere in considerazione l'uso di Ambari per la gestione dei cluster.
- Modificare l'archiviazione dei dati da HDFS locale a WASB o ADLS o AD FS per l'elaborazione di script.
- Prendere in considerazione l'uso di RBAC Ranger per le tabelle Hive e il controllo.
- Prendere in considerazione l'uso di CosmosDB al posto di MongoDB o Cassandra.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo successivo di questa serie:

- [Infrastructure best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-infrastructure.md) (Procedure consigliate per l'infrastruttura relative alla migrazione da locale ad Azure HDInsight Hadoop)
