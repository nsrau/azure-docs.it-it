---
title: Eseguire query sui dati da una risorsa di archiviazione di Azure compatibile con HDFS - Azure HDInsight
description: Informazioni su come eseguire query sui dati da Archiviazione di Azure e Azure Data Lake Storage per archiviare i risultati dell'analisi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: e62f6f8df51c6acf4e2ad8e28e431d264c2c99e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037250"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight

Per analizzare i dati nel cluster HDInsight, è possibile archiviare i dati in [Archiviazione di Azure,](../storage/common/storage-introduction.md) [Archiviazione di Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[o](../storage/blobs/data-lake-storage-introduction.md)in una combinazione. Queste opzioni di archiviazione consentono di eliminare in modo sicuro i cluster HDInsight usati per il calcolo senza perdere i dati utente.

Apache Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.6 è possibile selezionare Archiviazione di Azure o Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 come file system predefinito, con alcune eccezioni. Per il supporto dell'uso di Data Lake Storage Gen 1 come risorsa di archiviazione sia predefinita che collegata, vedere [Disponibilità per il cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Questo articolo illustra come usare Archiviazione di Azure con i cluster HDInsight. Per informazioni sull'uso di Data Lake Storage Gen 1 con i cluster HDInsight, vedere [Usare Azure Data Lake Storage con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> BlobStorage del tipo di account di archiviazione può essere usato solo come archiviazione secondaria per i cluster HDInsight.Storage account kind **BlobStorage** can only be used as secondary storage for HDInsight clusters.

| Tipo di account di archiviazione | Servizi supportati | Livelli di prestazioni supportati |Livelli di prestazioni non supportati| Livelli di accesso supportati |
|----------------------|--------------------|-----------------------------|---|------------------------|
| Archiviazione v2 (utilizzo generico V2)  | BLOB     | Standard                    |Premium| Caldo, Fresco, Archivio\*   |
| Archiviazione (utilizzo generico v1)   | BLOB     | Standard                    |Premium| N/D                    |
| BlobStorage                    | BLOB     | Standard                    |Premium| Caldo, Fresco, Archivio\*   |

Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Il contenitore predefinito include log di sistema e applicazioni. Assicurarsi di recuperare i log prima di eliminare il contenitore.

La condivisione di un contenitore BLOB come file system predefinito per più cluster non è supportata.

> [!NOTE]  
> Il livello di accesso all'archivio è un livello offline con una latenza di recupero di diverse ore e non è consigliato per l'uso con HDInsight.The Archive access tier is an offline tier that has a several hour retrieval latency and isn't recommended for use with HDInsight. Per altre informazioni, vedere [Livello di accesso archivio](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Accedere ai file dall'interno del clusterAccess files from within cluster

Esistono diversi modi per accedere ai file in Data Lake Storage da un cluster HDInsight. Lo schema URI offre l'accesso non crittografato (con il prefisso *wasb:*) e l'accesso con crittografia SSL (con il prefisso *wasbs*). Se possibile, è consigliabile usare *wasbs* anche per accedere ai dati presenti nella stessa area di Azure.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Uso del formato con percorso abbreviato**. Con questo approccio, si sostituisce il percorso fino alla radice del cluster con:With this approach, you replace the path up to the cluster root with:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono utilizzati tutti e tre gli schemi URI. Sostituire `CONTAINERNAME` `STORAGEACCOUNT` e con i valori pertinenti

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi hdfs

1. Creare un file semplice nell'archiviazione locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archiviazione cluster.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dall'archiviazione locale all'archiviazione cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archiviazione cluster.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Quando si usano i BLOB al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato WASB e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Creazione di una tabella HiveCreating a Hive table

Vengono visualizzate tre posizioni di file a scopo illustrativo. Per l'esecuzione effettiva, `LOCATION` utilizzare solo una delle voci.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Accedere ai file da un cluster esterno

Microsoft provides the following tools to work with Azure Storage:

| Strumento | Linux | OS X | WINDOWS |
| --- |:---:|:---:|:---:|
| [Portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfaccia della riga di comando di AzureAzure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Copia Azzurra](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identificare il percorso di archiviazione da AmbariIdentify storage path from Ambari

* Per identificare il percorso completo dell'archivio predefinito configurato, passare a:

    **HDFS** > **Configs** `fs.defaultFS` e inserire nella casella di input del filtro.

* Per verificare se l'archivio wasb è configurato come archivio secondario, passare a:To check if wasb store is configured as secondary storage, navigate to:

    **HDFS** > **Configs** `blob.core.windows.net` e inserire nella casella di input del filtro.

Per ottenere il percorso utilizzando l'API REST di Ambari, vedere [Ottenere l'archiviazione predefinita](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Contenitori BLOB

Per usare i BLOB, è innanzitutto necessario creare un [account di archiviazione di Azure.To](../storage/common/storage-create-storage-account.md)use blobs, you first create an Azure Storage account . Come parte della procedura, è necessario specificare l'area di Azure in cui viene creato l'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area del cluster. Il database di SQL Server del metastore Hive, inoltre, deve trovarsi nella stessa area del database di SQL Server del metastore Apache Oozie.

Ovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore presente nell'account di archiviazione di Azure. Può trattarsi di un contenitore BLOB esistente, creato all'esterno di HDInsight, oppure di un contenitore creato per un cluster HDInsight.

Il contenitore BLOB predefinito archivia informazioni specifiche del cluster come i log e la cronologia processo. Non condividere un contenitore BLOB predefinito con più cluster HDInsight. Questa operazione potrebbe danneggiare la cronologia processo. È consigliabile usare un contenitore diverso per ogni cluster e inserire i dati condivisi in un account di archiviazione collegato specificato nella distribuzione di tutti i cluster pertinenti anziché nell'account di archiviazione predefinito. Per altre informazioni sulla configurazione degli account di archiviazione collegati, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md). È comunque possibile riusare un contenitore di archiviazione predefinito dopo l'eliminazione del cluster HDInsight originale. Per i cluster HBase, è effettivamente possibile mantenere i dati e lo schema della tabella HBase creando un nuovo cluster HBase usando il contenitore BLOB predefinito usato da un cluster HBase eliminato.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Usare account di archiviazione aggiuntivi

Durante la creazione di un cluster HDInsight viene specificato l'account di Archiviazione di Azure da associare al cluster. Oltre a questo account di archiviazione, durante il processo di creazione o dopo la creazione di un cluster è possibile aggiungere altri account di archiviazione della stessa sottoscrizione di Azure o di sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come usare una risorsa di archiviazione di Azure compatibile con HDFS con HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Introduzione ad Azure HDInsightGet started with Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introduzione ad Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Caricare i dati in HDInsightUpload data to HDInsight](hdinsight-upload-data.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Esercitazione: Estrarre, trasformare e caricare dati usando Query interattiva in Azure HDInsightTutorial: Extract, transform, and load data using Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
