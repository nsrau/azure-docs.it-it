---
title: Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Azure Data Lake Storage Gen2
description: Introduzione all'interfaccia della riga di comando di Hadoop Distributed File System per Azure Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: f1f4cb036f4df226d651f8f4d0f5c7492f453a0a
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269741"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Se si dispone di un cluster HDInsight collegato o si esegue un processo di Apache Spark tramite Azure Databricks per eseguire l'analisi dei dati archiviati in un account di Archiviazione di Azure è possibile usare l'interfaccia della riga di comando (CLI) per recuperare e modificare i dati caricati.

## <a name="hdfs-cli-with-hdinsight"></a>Interfaccia della riga di comando di Hadoop Distributed File System con HDInsight

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Questo file system è accessibile mediante la shell che interagisce direttamente con Hadoop Distributed File System e altri file system supportati da Hadoop. Di seguito sono riportati i comandi usati più comunemente e i collegamenti a risorse utili.

>[!IMPORTANT]
>La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Per informazioni su come eliminare un cluster, vedere l'[articolo relativo all'argomento](../../hdinsight/hdinsight-delete-cluster.md). I dati archiviati in un account di archiviazione nel quale è abilitato Data Lake Storage Gen2 persistono anche dopo l'eliminazione di un cluster HDInsight.

### <a name="create-a-file-system"></a>Creare un file system

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Sostituire il segnaposto `<file-system-name>` con il nome che si vuole assegnare al file system.

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

### <a name="get-a-list-of-files-or-directories"></a>Ottenere un elenco di file o directory

    hdfs dfs -ls <path>

Sostituire il segnaposto `<path>` con l'URI del file system o della cartella del file system.

Ad esempio: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>Creare una directory

    hdfs dfs -mkdir [-p] <path>

Sostituire il segnaposto `<path>` con il nome del file system radice o di una cartella all'interno del file system.

Ad esempio: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>Eliminare un file o una directory

    hdfs dfs -rm <path>

Sostituire il segnaposto `<path>` con l'URI del file o della cartella che si vuole eliminare.

Ad esempio: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Usare l’infrastruttura CLI di HDFS con un cluster Hadoop di HDInsight in Linux

Per prima cosa stabilire l’[accesso remoto ai servizi](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Selezionando [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), il codice di PowerShell di esempio si presenta come segue:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
La stringa di connessione è reperibile nella sezione "SSH + Cluster login" del pannello del cluster HDInsight nel portale di Azure. Al momento della creazione del cluster sono state specificate le credenziali SSH.

Per altre informazioni sull'interfaccia della riga di comando di Hadoop Distributed File System, vedere la [documentazione ufficiale](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e la [Guida alle autorizzazioni di Hadoop Distributed File System](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Per altre informazioni sugli ACL in Databricks, vedere i [CLI segreti](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli).

## <a name="hdfs-cli-with-azure-databricks"></a>Interfaccia della riga di comando di Hadoop Distributed File System con Azure Databricks

Databricks fornisce un'interfaccia della riga di comando di semplice utilizzo che integra l'API REST Databricks. Il progetto open source è ospitato in [GitHub](https://github.com/databricks/databricks-cli). Di seguito sono riportati i comandi di uso comune.

### <a name="create-a-file-system"></a>Creare un file system

Inserire qui le istruzioni.

### <a name="get-a-list-of-files-or-directories"></a>Ottenere un elenco di file o directory

    dbfs ls [-l]

### <a name="create-a-directory"></a>Creare una directory

    dbfs mkdirs

### <a name="delete-a-file"></a>Eliminare un file

    dbfs rm [-r]

Un altro modo per interagire con Databricks è rappresentato dai notebook. Anche se un notebook ha un linguaggio principale, è possibile combinare più linguaggi specificando il comando magic %language all'inizio di una cella. In particolare, %sh consente di eseguire un codice shell nel notebook in modo molto simile al precedente esempio di HDInsight.

### <a name="get-a-list-of-files-or-directories"></a>Ottenere un elenco di file o directory

    %sh ls <args>

### <a name="create-a-directory"></a>Creare una directory

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Eliminare un file o una directory

    %sh rm [-skipTrash] URI [URI ...]

Dopo l'avvio del cluster Spark in Azure Databricks, verrà creato un nuovo notebook. Lo script notebook di esempio apparirà come segue:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Per altre informazioni sull'interfaccia della riga di comando di Databricks, vedere la [documentazione ufficiale](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Per altre informazioni sui notebook, vedere la sezione [notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) della documentazione.

## <a name="set-file-and-directory-level-permissions"></a>Impostare le autorizzazioni a livello di file e directory

Impostare e ottenere le autorizzazioni di accesso a livello di file e directory. Ecco alcuni comandi per iniziare. 

Per ulteriori informazioni sulle autorizzazioni a livello di file e directory per il filesystem Azure Data Lake Gen2, vedere [Controllo di accesso di Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visualizzare gli elenchi di controllo di accesso (ACL) di file e directory

    hdfs dfs -getfacl [-R] <path>

Esempio:

`hdfs dfs -getfacl -R /dir`

Vedere [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Impostare gli ACL su file e directory

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Esempio:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Vedere [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Modificare il proprietario dei file

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Vedere [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Modificare l'associazione del gruppo di file

    hdfs dfs -chgrp [-R] <group> <URI>

Vedere [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Modificare le autorizzazioni dei file

    hdfs dfs -chmod [-R] <mode> <URI>

Vedere [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

È possibile visualizzare l'elenco completo dei comandi nel sito Web della [Guida alla Shell del filesystem Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Passaggi successivi

[Usare un account con supporto di Azure Data Lake Storage Gen2 in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 
