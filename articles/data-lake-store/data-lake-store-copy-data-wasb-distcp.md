---
title: Copiare dati in e da WASB in Data Lake Store con Distcp| Microsoft Azure
description: Usare lo strumento Distcp per copiare i dati da e nel BLOB di Archiviazione di Azure ad Archivio Data Lake
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 1e66ce0af4d3d33b584f9f3a881c34aa04e783b7
ms.lasthandoff: 03/07/2017


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Usare Distcp per copiare dati tra i BLOB di archiviazione di Azure e Archivio Data Lake
> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Dopo avere creato un cluster HDInsight con accesso a un account Data Lake Store, è possibile usare gli strumenti dell'ecosistema Hadoop, ad esempio Distcp, per copiare i dati **da e in** una risorsa di archiviazione del cluster HDInsight in un account Data Lake Store. Questo articolo include istruzioni per eseguire questa operazione.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="do-you-learn-fast-with-videos"></a>Apprendimento rapido con i video
[Guardare questo video](https://mix.office.com/watch/1liuojvdx6sie) su come copiare dati tra i BLOB di archiviazione di Azure e Archivio Data Lake con DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare Distcp da un cluster HDInsight Linux

Un cluster HDInsight include l'utilità Distcp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Archivio Data Lake come risorsa di archiviazione aggiuntiva, l'utilità Distcp può essere usata senza modifiche anche per copiare dati in e da un account Archivio Data Lake. Questa sezione illustra come usare l'utilità Distcp.

1. Dal desktop usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect). Eseguire i comandi dal prompt SSH.

2. Verificare se è possibile accedere ai BLOB di archiviazione di Azure. Eseguire il comando seguente:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Dovrebbe essere visualizzato un elenco di contenuti del BLOB di archiviazione.
3. Analogamente, verificare se è possibile accedere all'account Archivio Data Lake dal cluster. Eseguire il comando seguente:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Dovrebbe essere visualizzato un elenco di file/cartelle nell'account Archivio Data Lake.
4. Usare Distcp per copiare i dati dal BLOB di archiviazione di Azure all'account Archivio Data Lake.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    I contenuti della cartella **/example/data/gutenberg/** in WASB verranno copiati in **/myfolder** nell'account Data Lake Store.
5. Analogamente, usare Distcp per copiare dati dall'account Archivio Data Lake al BLOB di archiviazione di Azure.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    I contenuti di **/myfolder** nell'account Data Lake Store verranno copiati nella cartella **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Dato che il livello di granularità minimo per DistCp corrisponde a un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per l'ottimizzazione per Data Lake Store. A tale scopo è possibile impostare il parametro del numero di mapper ('m') nella riga di comando. Questo parametro specifica il numero massimo di mapper che verranno usati per la copia dei dati. Il valore predefinito è 20.

**Esempio**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di mapper da usare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: determinare la memoria totale di YARN** - il primo passaggio consiste nel determinare la memoria di YARN disponibile per il cluster in cui viene eseguito il processo DistCp. Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda Configs (Configurazioni) per visualizzare la memoria di YARN. Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per ogni nodo per il numero di nodi nel cluster.

* **Passaggio 2: calcolare il numero di mapper** - il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Anche queste informazioni sono disponibili nel portale di Ambari. Passare a YARN e visualizzare la scheda Configs (Configurazioni). Le dimensioni del contenitore YARN sono visualizzate in questa finestra. L'equazione per ottenere il numero di mapper (**m**) è

        m = (number of nodes * YARN memory for each node) / YARN container size

**Esempio**

Si supponga di avere 4 nodi D14v2s nel cluster e di voler tentare il trasferimento di 10 TB di dati da 10 cartelle diverse. Ogni cartella contiene quantità variabili di dati e le dimensioni dei file all'interno di ogni cartella sono diverse.

* Memoria di YARN totale - Dal portale di Ambari si stabilisce che la memoria di YARN è pari a 96 GB per un nodo D14. Pertanto, la memoria totale di YARN per un cluster a 4 nodi è: 

        YARN memory = 4 * 96GB = 384GB

* Numero di mapper - Dal portale di Ambari si stabilisce che le dimensioni del contenitore YARN sono 3072 per un nodo del cluster D14. Il numero di mapper è quindi:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se altre applicazioni usano la memoria, è possibile scegliere di usare solo una parte della memoria di YARN del cluster per DistCp.

### <a name="copying-large-datasets"></a>Copia di set di dati di grandi dimensioni

Quando le dimensioni del set di dati da spostare sono molto grandi (ad esempio, maggiori di&1; TB) o se esistono molte cartelle diverse, è consigliabile usare più processi DistCp. Non si noterà probabilmente alcun miglioramento delle prestazioni, ma in questo modo è possibile distribuire i processi in modo che, se un processo non riesce, sia sufficiente riavviare tale processo specifico invece dell'intero processo.

### <a name="limitations"></a>Limitazioni

* DistCp tenta di creare mapper con dimensioni simili per ottimizzare le prestazioni. Un aumento del numero di mapper non sempre corrisponde a un miglioramento delle prestazioni.

* DistCp è limitato a un solo mapper per file, quindi non è possibile avere più mapper che file. Dato che DistCp può assegnare un solo mapper a un file, ciò comporta una limitazione alla concorrenza utilizzabile per la copia di file di grandi dimensioni.

* In presenza di un numero ridotto di file di grandi dimensioni, è consigliabile dividerli in blocchi di file da 256 MB per ottenere una maggiore concorrenza potenziale. 
 
* Se si esegue la copia da un account di Archiviazione BLOB di Azure, il processo di copia potrebbe essere limitato nell'ambito dell'archiviazione BLOB. In questo modo le prestazioni del processo di copia diminuiranno. Per altre informazioni sui limiti di Archiviazione BLOB di Azure, vedere i limiti di Archiviazione di Azure in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Vedere anche
* [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

