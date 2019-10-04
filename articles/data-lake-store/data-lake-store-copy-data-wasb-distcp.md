---
title: Copiare dati verso e da WASB in Data Lake Storage Gen1 di Azure con Distcp | Microsoft Docs
description: Usare lo strumento Distcp per copiare dati da e verso BLOB di Archiviazione di Microsoft Azure in Data Lake Storage Gen1 di Azure
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878801"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Usare Distcp per copiare dati tra i BLOB del servizio di Archiviazione di Microsoft Azure e i Data Lake Storage Gen1 di Azure
> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se si dispone di un cluster HDInsight con accesso ai Data Lake Storage Gen1 di Azure, è possibile usare gli strumenti dell'ecosistema Hadoop, ad esempio Distcp per copiare i dati **da e verso** un'archiviazione cluster HDInsight (WASB) in un account Data Lake Storage Gen1. Questo articolo include istruzioni sull'uso dello strumento DistCp.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare Distcp da un cluster HDInsight Linux

Un cluster HDInsight include l'utilità Distcp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, l'utilità DistCp può essere usata senza modifiche anche per copiare dati in e da un account Data Lake Storage Gen1. Questa sezione illustra come usare l'utilità DistCp.

1. Dal desktop usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Eseguire i comandi dal prompt SSH.

2. Verificare se è possibile accedere ai BLOB di archiviazione di Azure. Eseguire il comando seguente:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    L'output dovrebbe mostrare un elenco di contenuti del BLOB di archiviazione.

3. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage Gen1 dal cluster. Eseguire il comando seguente:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    L'output dovrebbe mostrare un elenco di file/cartelle nell'account Data Lake Storage Gen1.

4. Usare Distcp per copiare i dati dal BLOB del servizio di archiviazione di Azure (WASB) all'account Data Lake Storage Gen1.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Il comando copia i contenuti della cartella **/esempio/data/gutenberg/** della cartella in WASB in **/myfolder** nell'account Data Lake Storage Gen1.

5. Analogamente, usare Distcp per copiare dati dall'account Data Lake Storage Gen1 a WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Il comando copia i contenuti di **/myfolder** nell'account Data Lake Storage Gen1 nella cartella **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Poiché la granularità più bassa di DistCp è un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per ottimizzarlo rispetto a Data Lake Storage Gen1. Il numero di copie simultanee si controlla impostando il parametro del numero di mapper ('m') nella riga di comando. Questo parametro specifica il numero massimo di mapper usati per la copia dei dati. Il valore predefinito è 20.

**Esempio**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di mapper da usare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: Determinare la memoria YARN totale** -il primo passaggio consiste nel determinare la memoria di YARN disponibile per il cluster in cui viene eseguito il processo DistCp. Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda Configs (Configurazioni) per visualizzare la memoria di YARN. Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per ogni nodo per il numero di nodi nel cluster.

* **Passaggio 2: Calcolare il numero di mapper**. Il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Anche queste informazioni sono disponibili nel portale di Ambari. Passare a YARN e visualizzare la scheda Configs (Configurazioni). Le dimensioni del contenitore YARN sono visualizzate in questa finestra. L'equazione per ottenere il numero di mapper (**m**) è

        m = (number of nodes * YARN memory for each node) / YARN container size

**Esempio**

Si supponga di avere 4 nodi D14v2s nel cluster e di voler tentare il trasferimento di 10 TB di dati da 10 cartelle diverse. Ogni cartella contiene quantità variabili di dati e le dimensioni dei file all'interno di ogni cartella sono diverse.

* Memoria di YARN totale - Dal portale di Ambari si stabilisce che la memoria di YARN è pari a 96 GB per un nodo D14. Pertanto, la memoria totale di YARN per un cluster a quattro nodi è: 

        YARN memory = 4 * 96GB = 384GB

* Numero di mapper - Dal portale di Ambari si stabilisce che le dimensioni del contenitore YARN sono 3072 per un nodo del cluster D14. Il numero di mapper è quindi:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se altre applicazioni usano la memoria, è possibile scegliere di usare solo una parte della memoria di YARN del cluster per DistCp.

### <a name="copying-large-datasets"></a>Copia di set di dati di grandi dimensioni

Quando le dimensioni del set di dati da spostare sono molto grandi (ad esempio, maggiori di 1 TB) o se esistono molte cartelle diverse, è consigliabile usare più processi DistCp. Non si noterà probabilmente alcun miglioramento delle prestazioni, ma in questo modo è possibile distribuire i processi in modo che, se un processo non riesce, sia sufficiente riavviare tale processo specifico invece dell'intero processo.

### <a name="limitations"></a>Limitazioni

* DistCp tenta di creare mapper con dimensioni simili per ottimizzare le prestazioni. Un aumento del numero di mapper non sempre corrisponde a un miglioramento delle prestazioni.

* DistCp è limitato a un solo mapper per file, quindi non è possibile avere più mapper che file. Dato che DistCp può assegnare un solo mapper a un file, ciò comporta una limitazione alla concorrenza utilizzabile per la copia di file di grandi dimensioni.

* In presenza di un numero ridotto di file di grandi dimensioni, è consigliabile dividerli in blocchi di file da 256 MB per ottenere una maggiore concorrenza potenziale. 
 
* Se si esegue la copia da un account di Archiviazione BLOB di Azure, il processo di copia potrebbe essere limitato nell'ambito dell'archiviazione BLOB. In questo caso le prestazioni del processo di copia diminuiscono. Per altre informazioni sui limiti di Archiviazione BLOB di Azure, vedere i limiti di Archiviazione di Azure in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Vedere anche
* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
