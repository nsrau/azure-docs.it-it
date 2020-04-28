---
title: Copiare dati da e verso WASB in Azure Data Lake Storage Gen1 usando DistCp
description: Usare lo strumento DistCp per copiare i dati da e verso i BLOB di archiviazione di Azure in Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638834"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Usare DistCp per copiare dati tra i BLOB di archiviazione di Azure e Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se si dispone di un cluster HDInsight con accesso a Azure Data Lake Storage Gen1, è possibile usare gli strumenti dell'ecosistema Hadoop, ad esempio DistCp, per copiare dati da e verso un'archiviazione cluster HDInsight (WASB) in un account Data Lake Storage Gen1. Questo articolo illustra come usare lo strumento DistCp.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare DistCp da un cluster HDInsight Linux

HDInsight An cluster viene fornita con lo strumento DistCp, che può essere usato per copiare dati da origini diverse in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, è possibile usare DistCp predefinito per copiare i dati da e verso un account di Data Lake Storage Gen1. In questa sezione viene illustrato come usare lo strumento DistCp.

1. Dal desktop usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Eseguire i comandi dal prompt SSH.

1. Verificare se è possibile accedere ai BLOB di archiviazione di Azure (WASB). Eseguire il comando seguente:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   L'output fornisce un elenco di contenuti nel BLOB di archiviazione.

1. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage Gen1 dal cluster. Eseguire il comando seguente:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    L'output fornisce un elenco di file e cartelle nell'account Data Lake Storage Gen1.

1. Usare DistCp per copiare dati da WASB a un account Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Il comando copia i contenuti della cartella **/esempio/data/gutenberg/** della cartella in WASB in **/myfolder** nell'account Data Lake Storage Gen1.

1. Analogamente, usare DistCp per copiare i dati da un account Data Lake Storage Gen1 a WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Il comando copia il contenuto di **/MyFolder** nell'account data Lake storage Gen1 nella cartella **/example/data/Gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Poiché la granularità più bassa dello strumento DistCp è un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per ottimizzarlo rispetto a Data Lake Storage Gen1. È possibile controllare il numero di copie simultanee impostando il parametro del numero di Mapper (' m') nella riga di comando. Questo parametro specifica il numero massimo di mapper usati per la copia dei dati. Il valore predefinito è 20.

Esempio:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di Mapper da usare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: determinare la memoria totale di YARN** - il primo passaggio consiste nel determinare la memoria di YARN disponibile per il cluster in cui viene eseguito il processo DistCp. Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda **configs (configurazioni** ) per visualizzare la memoria Yarn. Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per ogni nodo per il numero di nodi nel cluster.

* **Passaggio 2: calcolare il numero di mapper** - il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Le informazioni sulle dimensioni del contenitore YARN sono disponibili anche nel portale di Ambari. Passare a YARN e visualizzare la scheda **configs (configurazioni** ). Le dimensioni del contenitore YARN vengono visualizzate in questa finestra. L'equazione da raggiungere al numero di Mapper (**m**) è:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Esempio:

Si supponga di avere quattro nodi D14v2s nel cluster e di voler trasferire 10 TB di dati da 10 cartelle diverse. Ogni cartella contiene quantità variabili di dati e le dimensioni dei file all'interno di ogni cartella sono diverse.

* Memoria di YARN totale - Dal portale di Ambari si stabilisce che la memoria di YARN è pari a 96 GB per un nodo D14. Pertanto, la memoria totale di YARN per un cluster a quattro nodi è:

   `YARN memory = 4 * 96GB = 384GB`

* Numero di mapper - Dal portale di Ambari si stabilisce che le dimensioni del contenitore YARN sono 3072 per un nodo del cluster D14. Il numero di Mapper è il seguente:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Se altre applicazioni usano memoria, è possibile scegliere di usare solo una parte della memoria YARN del cluster per DistCp.

### <a name="copying-large-datasets"></a>Copia di set di dati di grandi dimensioni

Quando la dimensione del set di dati da spostare è grande (ad esempio > 1 TB) o se si dispone di molte cartelle diverse, è consigliabile usare più processi DistCp. È probabile che non si verifichi un miglioramento delle prestazioni, ma distribuisce i processi in modo che, in caso di esito negativo di un processo, sia necessario riavviare solo quel processo specifico anziché l'intero processo.

### <a name="limitations"></a>Limitazioni

* DistCp tenta di creare mapper con dimensioni simili per ottimizzare le prestazioni. Un aumento del numero di mapper non sempre corrisponde a un miglioramento delle prestazioni.

* DistCp è limitato a un solo mapper per file, Di conseguenza, non è necessario disporre di più Mapper rispetto ai file. Poiché DistCp può assegnare un solo Mapper a un file, questo limita la quantità di concorrenza che può essere usata per copiare file di grandi dimensioni.

* Se si dispone di un numero ridotto di file di grandi dimensioni, suddividerli in blocchi di file da 256 MB per ottenere una maggiore concorrenza potenziale.

* Se si esegue la copia da un account di archiviazione BLOB di Azure, il processo di copia potrebbe essere limitato al lato dell'archiviazione BLOB. In questo caso le prestazioni del processo di copia diminuiscono. Per altre informazioni sui limiti dell'archiviazione BLOB di Azure, vedere limiti di archiviazione di Azure in [sottoscrizione di Azure e limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Vedere anche

* [Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
