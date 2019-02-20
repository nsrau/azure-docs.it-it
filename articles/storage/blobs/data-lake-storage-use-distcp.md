---
title: Usare DistCp per copiare i dati in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Usare lo strumento DistCp per copiare i dati da e in Data Lake Storage Gen2
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 8328ea5afb66bbecdafbb06dcbf6700194d62f9e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864061"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Usare DistCp per copiare dati tra i BLOB del servizio di Archiviazione di Microsoft Azure e i Data Lake Storage Gen2 di Azure

È possibile usare [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) per copiare i dati tra un account di archiviazione per utilizzo generico V2 e un account di archiviazione per utilizzo generico V2 con lo spazio dei nomi gerarchico abilitato. Questo articolo include istruzioni sull'uso dello strumento DistCp.

DistCp fornisce una serie di parametri della riga di comando e si consiglia di leggere questo articolo per ottimizzarne l'utilizzo. Questo articolo illustra le funzionalità di base e, in particolare, illustra la copia dei dati in un account abilitato per lo spazio dei nomi gerarchico.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di archiviazione di Azure senza le funzionalità di Data Lake Storage Gen2 (spazio dei nomi gerarchico) abilitate**.
* **Un account di Archiviazione di Azure con la funzionalità Data Lake Storage Gen2 abilitata**. Per istruzioni sulla creazione di un account, vedere [Creare un account di archiviazione di Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Un file system** creato nell'account di archiviazione con lo spazio dei nomi gerarchico abilitato.
* **Cluster HDInsight di Azure** con accesso a un account di archiviazione con Data Lake Storage Gen2 abilitato. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](data-lake-storage-use-hdi-cluster.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usare DistCp da un cluster HDInsight Linux

Un cluster HDInsight include l'utilità DistCp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Archiviazione BLOB di Azure e Azure Data Lake Storage insieme, l'utilità DistCp può essere usata senza modifiche anche per copiare dati dall'uno all'altro. Questa sezione illustra come usare l'utilità DistCp.

1. Creare una sessione SSH al cluster HDI. Vedere [Connettersi a un cluster HDInsight basato su Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verificare se è possibile accedere all'account per utilizzo generico V2 esistente (senza spazio dei nomi gerarchico abilitato).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    L'output dovrebbe mostrare un elenco di contenuti del contenitore.

3. Analogamente, verificare se è possibile accedere all'account di archiviazione con lo spazio dei nomi gerarchico abilitato dal cluster. Eseguire il comando seguente:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    L'output dovrebbe mostrare un elenco di file/cartelle nell'account Data Lake Storage.

4. Usare DistCp per copiare i dati dal BLOB del servizio di archiviazione di Azure all'account Data Lake Storage.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Il comando copia i contenuti della cartella **/example/data/gutenberg/** dell'archivio BLOB in **/myfolder** nell'account Data Lake Storage.

5. Analogamente, usare DistCp per copiare dati dall'account Data Lake Storage al BLOB di archiviazione, ovvero WASB.

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Il comando copia i contenuti di **/myfolder** nell'account Data Lake Store nella cartella **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerazioni sulle prestazioni per l'uso di DistCp

Dato che il livello di granularità minimo per DistCp corrisponde a un singolo file, l'impostazione del numero massimo di copie simultanee è il parametro più importante per eseguire l'ottimizzazione per Data Lake Storage. Il numero di copie simultanee è uguale al numero di mapper (**m**) nella riga di comando. Questo parametro specifica il numero massimo di mapper usati per la copia dei dati. Il valore predefinito è 20.

**Esempio**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Come determinare il numero di mapper da usare

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: Determinare la memoria totale disponibile per la coda di app YARN "predefinita"**. Il primo passaggio consiste nel determinare la memoria disponibile per la coda di app YARN "predefinita". Queste informazioni sono disponibili nel portale di Ambari associato al cluster. Passare a YARN e visualizzare la scheda Configs (Configurazioni) per visualizzare la memoria di YARN disponibile per la coda di app "predefinita". Si tratta della memoria totale disponibile per il processo DistCp (che è di fatto un processo MapReduce).

* **Passaggio 2: Calcolare il numero di mapper**. Il valore di **m** è uguale al quoziente della memoria totale di YARN divisa per le dimensioni del contenitore YARN. Anche queste informazioni sono disponibili nel portale di Ambari. Passare a YARN e visualizzare la scheda Configs (Configurazioni). Le dimensioni del contenitore YARN sono visualizzate in questa finestra. L'equazione per ottenere il numero di mapper (**m**) è

        m = (number of nodes * YARN memory for each node) / YARN container size

**Esempio**

Si supponga di avere un cluster con 4 nodi D14v2 e di voler tentare il trasferimento di 10 TB di dati da 10 cartelle diverse. Ogni cartella contiene quantità variabili di dati e le dimensioni dei file all'interno di ogni cartella sono diverse.

* **Memoria totale di YARN**: dal portale di Ambari si stabilisce che la memoria di YARN è pari a 96 GB per un nodo D14. Pertanto, la memoria totale di YARN per un cluster a quattro nodi è: 

        YARN memory = 4 * 96GB = 384GB

* **Numero di mapper**: dal portale di Ambari si stabilisce che le dimensioni del contenitore YARN sono pari a 3.072 MB per un nodo del cluster D14. Il numero di mapper è quindi:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se altre applicazioni usano la memoria, è possibile scegliere di usare solo una parte della memoria di YARN del cluster per DistCp.

### <a name="copying-large-datasets"></a>Copia di set di dati di grandi dimensioni

Quando le dimensioni del set di dati da spostare sono molto grandi (ad esempio, maggiori di 1 TB) o se esistono molte cartelle diverse, è consigliabile usare più processi DistCp. Non si noterà probabilmente alcun miglioramento delle prestazioni, ma in questo modo è possibile distribuire i processi in modo che, se un processo non riesce, sia sufficiente riavviare tale processo specifico invece dell'intero processo.

### <a name="limitations"></a>Limitazioni

* DistCp tenta di creare mapper con dimensioni simili per ottimizzare le prestazioni. Un aumento del numero di mapper non sempre corrisponde a un miglioramento delle prestazioni.

* DistCp è limitato a un solo mapper per file, quindi non è possibile avere più mapper che file. Dato che DistCp può assegnare un solo mapper a un file, ciò comporta una limitazione alla concorrenza utilizzabile per la copia di file di grandi dimensioni.

* In presenza di un numero ridotto di file di grandi dimensioni, è consigliabile dividerli in blocchi di file da 256 MB per ottenere una maggiore concorrenza potenziale.
