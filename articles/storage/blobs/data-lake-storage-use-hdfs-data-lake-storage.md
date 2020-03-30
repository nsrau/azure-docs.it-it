---
title: Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Azure Data Lake Storage Gen2
description: Introduzione all'interfaccia della riga di comando di Hadoop Distributed File System per Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992213"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2

È possibile accedere e gestire i dati nell'account di archiviazione utilizzando un'interfaccia della riga di comando come si farebbe con un [file system distribuito Hadoop (HDFS, Hadoop Distributed File System).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Questo articolo fornisce alcuni esempi che ti aiuteranno a iniziare.

HDInsight fornisce l'accesso al contenitore distribuito collegato localmente ai nodi di calcolo. È possibile accedere a questo contenitore utilizzando la shell che interagisce direttamente con l'HDFS e gli altri file system supportati da Hadoop.

Per ulteriori informazioni sull'interfaccia della riga di comando HDFS, vedere la [documentazione ufficiale](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e la Guida alle [autorizzazioni HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Se si usa Azure Databricks anziché HDInsight e si vuole interagire con i dati usando un'interfaccia della riga di comando, è possibile usare l'interfaccia della riga di comando databricks per interagire con il file system Databricks.If you're using Azure Databricks instead of HDInsight, and you want to interact with your data by using a command line interface, you can use the Databricks CLI to interact with the Databricks file system. Vedere [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Usare l’infrastruttura CLI di HDFS con un cluster Hadoop di HDInsight in Linux

Per prima cosa stabilire l’[accesso remoto ai servizi](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Selezionando [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), il codice di PowerShell di esempio si presenta come segue:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
La stringa di connessione è reperibile nella sezione "SSH + Cluster login" del pannello del cluster HDInsight nel portale di Azure. Al momento della creazione del cluster sono state specificate le credenziali SSH.

>[!IMPORTANT]
>La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Per informazioni su come eliminare un cluster, vedere l'[articolo relativo all'argomento](../../hdinsight/hdinsight-delete-cluster.md). I dati archiviati in un account di archiviazione nel quale è abilitato Data Lake Storage Gen2 persistono anche dopo l'eliminazione di un cluster HDInsight.

## <a name="create-a-container"></a>Creare un contenitore

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Sostituire il segnaposto `<container-name>` con il nome che si vuole assegnare al contenitore.

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

## <a name="get-a-list-of-files-or-directories"></a>Ottenere un elenco di file o directory

    hdfs dfs -ls <path>

Sostituire `<path>` il segnaposto con l'URI del contenitore o della cartella del contenitore.

Ad esempio: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Creare una directory

    hdfs dfs -mkdir [-p] <path>

Sostituire `<path>` il segnaposto con il nome del contenitore radice o una cartella all'interno del contenitore.

Ad esempio: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Eliminare un file o una directory

    hdfs dfs -rm <path>

Sostituire il segnaposto `<path>` con l'URI del file o della cartella che si vuole eliminare.

Ad esempio: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visualizzare gli elenchi di controllo di accesso (ACL) di file e directory

    hdfs dfs -getfacl [-R] <path>

Esempio:

`hdfs dfs -getfacl -R /dir`

Vedere [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Impostare gli ACL su file e directory

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Esempio:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Vedere [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Modificare il proprietario dei file

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Vedere [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Modificare l'associazione del gruppo di file

    hdfs dfs -chgrp [-R] <group> <URI>

Vedere [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Modificare le autorizzazioni dei file

    hdfs dfs -chmod [-R] <mode> <URI>

Vedere [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

È possibile visualizzare l'elenco completo dei comandi nel sito Web della [Guida alla Shell del filesystem Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Passaggi successivi

* [Usare un account con supporto di Azure Data Lake Storage Gen2 in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Informazioni sugli elenchi di controllo di accesso su file e directory](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
