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
ms.date: 10/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: dcab09385c6a664186d124ce3e042cc1fac82bd6


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
* **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## <a name="do-you-learn-fast-with-videos"></a>Apprendimento rapido con i video
[Guardare questo video](https://mix.office.com/watch/1liuojvdx6sie) su come copiare dati tra i BLOB di archiviazione di Azure e Data Lake Store con DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Usare Distcp da Desktop remoto (cluster Windows) o SSH (cluster Linux)
Un cluster HDInsight include l'utilità Distcp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Archivio Data Lake come risorsa di archiviazione aggiuntiva, l'utilità Distcp può essere usata senza modifiche anche per copiare dati in e da un account Archivio Data Lake. Questa sezione illustra come usare l'utilità Distcp.

1. Se si usa un cluster Windows, stabilire una connessione remota a un cluster HDInsight che ha accesso a un account Archivio Data Lake. Per le istruzioni, vedere [Connettersi a cluster con RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Dal desktop del cluster aprire la riga di comando di Hadoop.

    Se si usa un cluster Linux, usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Eseguire i comandi dal prompt SSH.
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

## <a name="see-also"></a>Vedere anche
* [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


