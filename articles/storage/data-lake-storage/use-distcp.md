---
title: Usare Distcp per copiare i dati nell'anteprima di Azure Data Lake Storage Gen2 | Microsoft Docs
description: Usare lo strumento Distcp per copiare i dati da e nell'anteprima di Data Lake Storage Gen2
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 2a958ceb0b3a1db9d06d045a8161fa6cd3ef5aba
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059927"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Usare Distcp per copiare dati tra i BLOB del servizio di archiviazione di Azure e l'anteprima di Data Lake Storage Gen2

Se si dispone di un cluster HDInsight con accesso all'anteprima di Azure Data Lake Storage Gen2, è possibile usare gli strumenti dell'ecosistema Hadoop, come Distcp, per copiare dati **da e verso** un'archiviazione cluster di HDInsight, ovvero WASB, in un account con supporto di Data Lake Storage Gen2. Questo articolo include istruzioni sull'uso dello strumento DistCp.

## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archiviazione di Azure con la funzionalità Azure Data Lake Storage (anteprima) abilitata**. Per istruzioni su come crearne uno, vedere [TODO](quickstart-create-account.md)
* **Cluster HDInsight di Azure** con accesso a un account di Data Lake Storage. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](use-hdi-cluster.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare Distcp da un cluster HDInsight Linux

Un cluster HDInsight include l'utilità Distcp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Archiviazione BLOB di Azure e Azure Data Lake Storage insieme, l'utilità Distcp può essere usata senza modifiche anche per copiare dati dall'uno all'altro. Questa sezione illustra come usare l'utilità DistCp.

1. Dal desktop usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Eseguire i comandi dal prompt SSH.

2. Verificare se è possibile accedere ai BLOB di archiviazione di Azure. Eseguire il comando seguente:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    L'output dovrebbe mostrare un elenco di contenuti del BLOB di archiviazione.

3. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage dal cluster. Eseguire il comando seguente:

        hdfs dfs -ls abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/

    L'output dovrebbe mostrare un elenco di file/cartelle nell'account Data Lake Storage.

4. Usare Distcp per copiare i dati dal BLOB del servizio di archiviazione di Azure all'account Data Lake Storage.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder

    Il comando copia i contenuti della cartella **/example/data/gutenberg/** dell'archivio BLOB in **/myfolder** nell'account Data Lake Storage.

5. Analogamente, usare Distcp per copiare dati dall'account Data Lake Storage al BLOB di archiviazione, ovvero WASB.

        hadoop distcp abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Il comando copia i contenuti di **/myfolder** nell'account Data Lake Store nella cartella **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Dato che il livello di granularità minimo per DistCp corrisponde a un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per eseguire l'ottimizzazione per Data Lake Storage. Il numero di copie simultanee si controlla impostando il parametro del numero di mapper ('m') nella riga di comando. Questo parametro specifica il numero massimo di mapper usati per la copia dei dati. Il valore predefinito è 20.

**Esempio**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di mapper da usare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: determinare la memoria totale di YARN** - il primo passaggio consiste nel determinare la memoria di YARN disponibile per il cluster in cui viene eseguito il processo DistCp. Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda Configs (Configurazioni) per visualizzare la memoria di YARN. Per ottenere la memoria totale di YARN, moltiplicare la memoria di YARN per ogni nodo per il numero di nodi nel cluster.

* **Passaggio 2: calcolare il numero di mapper** - il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Anche queste informazioni sono disponibili nel portale di Ambari. Passare a YARN e visualizzare la scheda Configs (Configurazioni). Le dimensioni del contenitore YARN sono visualizzate in questa finestra. L'equazione per ottenere il numero di mapper (**m**) è

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
