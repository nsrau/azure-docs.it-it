<properties 
   pageTitle="Copiare dati in e dal BLOB di archiviazione di Azure in Archivio Data Lake usando Distcp| Microsoft Azure"
   description="Usare lo strumento Distcp per copiare i dati da e nel BLOB di Archiviazione di Azure ad Archivio Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Usare Distcp per copiare dati tra i BLOB di archiviazione di Azure e Archivio Data Lake

Dopo avere creato un cluster HDInsight con accesso a un account Archivio Data Lake, è possibile usare gli strumenti dell'ecosistema Hadoop, ad esempio Distcp, per copiare i dati **da e in** una risorsa di archiviazione del cluster HDInsight in un account Archivio Data Lake. Questo articolo include istruzioni per eseguire questa operazione.

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup). 
- **Cluster Azure HDInsight** con accesso all'account Archivio Data Lake. Vedere [Creare un cluster HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.

## Usare Distcp da Desktop remoto (cluster Windows) o SSH (cluster Linux)

Un cluster HDInsight include l'utilità Distcp, che può essere usata per copiare i dati da diverse origini in un cluster HDInsight. Se il cluster HDInsight è stato configurato per l'uso di Archivio Data Lake come risorsa di archiviazione aggiuntiva, l'utilità Distcp può essere usata senza modifiche anche per copiare dati in e da un account Archivio Data Lake. Questa sezione illustra come usare l'utilità Distcp.

1. Se si usa un cluster Windows, stabilire una connessione remota a un cluster HDInsight che ha accesso a un account Archivio Data Lake. Per istruzioni, vedere [Connettersi a cluster con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Dal desktop del cluster aprire la riga di comando di Hadoop.

	Se si usa un cluster Linux, usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Eseguire i comandi dal prompt SSH.

3. Verificare se è possibile accedere ai BLOB di archiviazione di Azure. Eseguire il comando seguente:

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Dovrebbe essere visualizzato un elenco di contenuti del BLOB di archiviazione.

4. Analogamente, verificare se è possibile accedere all'account Archivio Data Lake dal cluster. Eseguire il comando seguente:

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Dovrebbe essere visualizzato un elenco di file/cartelle nell'account Archivio Data Lake.

5. Usare Distcp per copiare i dati dal BLOB di archiviazione di Azure all'account Archivio Data Lake.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	I contenuti della cartella **/example/data/gutenberg/** nel BLOB di archiviazione di Azure verranno copiati in **/myfolder** nell'account Archivio Data Lake.

6. Analogamente, usare Distcp per copiare dati dall'account Archivio Data Lake al BLOB di archiviazione di Azure.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	I contenuti di **/myfolder** nell'account Archivio Data Lake verranno copiati nella cartella **/example/data/gutenberg/** nel BLOB di archiviazione di Azure.

## Vedere anche

- [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->