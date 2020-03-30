---
title: Copiare i dati da e verso WASB in Azure Data Lake Storage Gen1 usando DistCpCopy data to and from WASB into Azure Data Lake Storage Gen1 using DistCp
description: Usare lo strumento DistCp per copiare dati da e verso i BLOB di Archiviazione di Azure in Azure Data Lake Storage Gen1Use the DistCp tool to copy data to and from Azure Storage blobs to Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638834"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Usare DistCp per copiare i dati tra i BLOB di Archiviazione di Azure e Azure Data Lake Storage Gen1Use DistCp to copy data between Azure Storage blobs and Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se si dispone di un cluster HDInsight con accesso ad Azure Data Lake Storage Gen1, è possibile usare gli strumenti dell'ecosistema Hadoop come DistCp per copiare i dati da e verso un'archiviazione cluster HDInsight (WASB) in un account Data Lake Storage Gen1. In questo articolo viene illustrato come utilizzare lo strumento DistCp.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure.** Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare DistCp da un cluster HDInsight Linux

Un cluster HDInsight viene fornito con lo strumento DistCp, che può essere usato per copiare dati da origini diverse in un cluster HDInsight.An HDInsight cluster comes with the DistCp tool, which can be used to copy data from different sources into an HDInsight cluster. Se il cluster HDInsight è stato configurato per l'uso di Data Lake Storage Gen1 come spazio di archiviazione aggiuntivo, è possibile usare DistCp out-of-the-box per copiare i dati da e verso un account Data Lake Storage Gen1. In questa sezione viene illustrato come utilizzare lo strumento DistCp.

1. Dal desktop usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Eseguire i comandi dal prompt SSH.

1. Verificare se è possibile accedere ai BLOB di Archiviazione di Azure (WASB). Eseguire il comando seguente:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   L'output fornisce un elenco di contenuti nel BLOB di archiviazione.

1. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage Gen1 dal cluster. Eseguire il comando seguente:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    L'output fornisce un elenco di file e cartelle nell'account Data Lake Storage Gen1.

1. Usare DistCp per copiare i dati da WASB a un account Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Il comando copia i contenuti della cartella **/esempio/data/gutenberg/** della cartella in WASB in **/myfolder** nell'account Data Lake Storage Gen1.

1. Analogamente, utilizzare DistCp per copiare i dati da un account Data Lake Storage Gen1 a WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Il comando copia il contenuto di **/myfolder** nell'account Data Lake Storage Gen1 nella cartella **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Poiché la granularità più bassa dello strumento DistCp è un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per ottimizzarlo rispetto a Data Lake Storage Gen1. È possibile controllare il numero di copie simultanee impostando il parametro del numero di mappatori ('m') nella riga di comando. Questo parametro specifica il numero massimo di mapper usati per la copia dei dati. Il valore predefinito è 20.

Esempio:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di mappatori da utilizzare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: determinare la memoria totale di YARN** - il primo passaggio consiste nel determinare la memoria di YARN disponibile per il cluster in cui viene eseguito il processo DistCp. Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda **Configs** per visualizzare la memoria YARN. Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per ogni nodo per il numero di nodi nel cluster.

* **Passaggio 2: calcolare il numero di mapper** - il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Le informazioni sulle dimensioni dei contenitori YARN sono disponibili anche nel portale Ambari. Passare a YARN e visualizzare la scheda **Configs (Configurazioni).** La dimensione del contenitore YARN viene visualizzata in questa finestra. L'equazione per arrivare al numero di mappatori (**m**) è:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Esempio:

Si supponga di disporre di quattro d14v2 nel cluster e di voler trasferire 10 TB di dati da 10 cartelle diverse. Ogni cartella contiene quantità variabili di dati e le dimensioni dei file all'interno di ogni cartella sono diverse.

* Memoria di YARN totale - Dal portale di Ambari si stabilisce che la memoria di YARN è pari a 96 GB per un nodo D14. Pertanto, la memoria totale di YARN per un cluster a quattro nodi è:

   `YARN memory = 4 * 96GB = 384GB`

* Numero di mapper - Dal portale di Ambari si stabilisce che le dimensioni del contenitore YARN sono 3072 per un nodo del cluster D14. Quindi, il numero di mappatori è:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Se altre applicazioni utilizzano memoria, è possibile scegliere di utilizzare solo una parte della memoria YARN del cluster per DistCp.

### <a name="copying-large-datasets"></a>Copia di set di dati di grandi dimensioni

Quando le dimensioni del set di dati da spostare sono grandi (ad esempio, > 1 TB) o se si dispone di molte cartelle diverse, è consigliabile usare più processi DistCp. È probabile che non vi sia alcun miglioramento delle prestazioni, ma distribuisce i processi in modo che se un processo non riesce, è necessario riavviare solo il processo specifico anziché l'intero processo.

### <a name="limitations"></a>Limitazioni

* DistCp tenta di creare mapper con dimensioni simili per ottimizzare le prestazioni. Un aumento del numero di mapper non sempre corrisponde a un miglioramento delle prestazioni.

* DistCp è limitato a un solo mapper per file, Pertanto, non dovresti avere più mappatori di quanti ne abbia. Poiché DistCp può assegnare un solo mapper a un file, questo limita la quantità di concorrenza che può essere utilizzata per copiare file di grandi dimensioni.

* Se si dispone di un numero ridotto di file di grandi dimensioni, suddividerli in blocchi di file da 256 MB per offrire una concorrenza potenziale maggiore.

* Se si sta copiando da un account di archiviazione BLOB di Azure, il processo di copia potrebbe essere limitato sul lato archiviazione BLOB. In questo caso le prestazioni del processo di copia diminuiscono. Per altre informazioni sui limiti dell'archiviazione BLOB di Azure, vedere Limiti di Archiviazione di Azure in Limiti della sottoscrizione e del servizio di Azure.To learn more about the limits of Azure Blob storage, see Azure Storage limits at [Azure subscription and service limits](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Vedere anche

* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
