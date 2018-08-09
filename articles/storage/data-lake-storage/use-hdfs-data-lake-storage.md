---
title: Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2
description: Introduzione all'interfaccia della riga di comando di Hadoop Distributed File System per Anteprima di Azure Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: ef28468c7aa420c145f5e6d79e2b079bd1d094f4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523683"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2

Anteprima di Azure Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Se si dispone di un cluster HDInsight collegato o si esegue un processo di Apache Spark tramite Azure Databricks per eseguire l'analisi dei dati archiviati in Azure Data Lake Storage Gen2, è possibile usare l'interfaccia della riga di comando (CLI) per recuperare e modificare i dati caricati. Nella parte restante dell'articolo vengono descritte le opzioni disponibili quando il [team di Archiviazione di Azure sta lavorando all'aggiunta di supporto per Azure Storage Explorer e il portale di Azure](https://azure.microsoft.com/roadmap/).

## <a name="hdfs-cli-with-hdinsight"></a>Interfaccia della riga di comando di Hadoop Distributed File System con HDInsight

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Questo file system è accessibile mediante la shell che interagisce direttamente con Hadoop Distributed File System e altri file system supportati da Hadoop. Di seguito sono riportati i comandi usati più comunemente e i collegamenti a risorse utili.

>[!IMPORTANT]
>La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più (informazioni su come [eliminare un cluster](../../hdinsight/hdinsight-delete-cluster.md)). Tuttavia, i dati archiviati in Azure Data Lake Storage Gen2 persistono anche dopo l'eliminazione di un cluster HDInsight.

Per ottenere un elenco di file o directory:

    hdfs dfs -ls <args>
Per creare una directory:

    hdfs dfs -mkdir [-p] <paths>
Per eliminare un file o una directory:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Nell'esempio verrà usato il cluster Hadoop HDInsight su Linux. Per usare l'interfaccia della riga di comando di Hadoop Distributed File System, è necessario innanzitutto stabilire [l'accesso remoto ai servizi](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Selezionando [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), il codice di PowerShell di esempio si presenta come segue:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

La stringa di connessione è reperibile nella sezione "SSH + Cluster login" del pannello del cluster HDInsight nel portale di Azure. Al momento della creazione del cluster sono state specificate le credenziali SSH.

Per altre informazioni sull'interfaccia della riga di comando di Hadoop Distributed File System, vedere la [documentazione ufficiale](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e la [Guida alle autorizzazioni di Hadoop Distributed File System](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>Interfaccia della riga di comando di Hadoop Distributed File System con Azure Databricks

Databricks fornisce un'interfaccia della riga di comando di semplice utilizzo che integra l'API REST Databricks. Il progetto open source è ospitato in [GitHub](https://github.com/databricks/databricks-cli). Di seguito sono riportati i comandi di uso comune.

Per ottenere un elenco di file o directory:

    dbfs ls [-l]
Per creare una directory:

    dbfs mkdirs
Per eliminare un file:

    dbfs rm [-r]

Un altro modo per interagire con Databricks è rappresentato dai notebook. Anche se un notebook ha un linguaggio principale, è possibile combinare più linguaggi specificando il comando magic %language all'inizio di una cella. In particolare, %sh consente di eseguire un codice shell nel notebook in modo molto simile al precedente esempio di HDInsight.

Per ottenere un elenco di file o directory:

    %sh ls <args>
Per creare una directory:

    %sh mkdir [-p] <paths>
Per eliminare un file o una directory:

    %sh rm [-skipTrash] URI [URI ...]

Dopo l'avvio del cluster Spark in Azure Databricks, verrà creato un nuovo notebook. Lo script notebook di esempio apparirà come segue:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Per altre informazioni sull'interfaccia della riga di comando di Databricks, vedere la [documentazione ufficiale](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Per altre informazioni sui notebook, vedere la sezione [notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) della documentazione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un cluster HDInsight con Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Usare un account con supporto di Azure Data Lake Storage Gen2 in Azure Databricks](./quickstart-create-databricks-account.md) 