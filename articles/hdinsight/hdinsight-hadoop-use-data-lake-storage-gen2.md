---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: cc17cd23ae197db25fed440eb249f2cf069d4859
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744592"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

[Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md) è un servizio di archiviazione cloud dedicato a Big Data Analytics, basato sull' [archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 combina le funzionalità di archiviazione BLOB e Azure Data Lake Storage Gen1 di Azure. Il servizio risultante offre funzionalità da Azure Data Lake Storage Gen1 tra cui: file system semantica, sicurezza a livello di directory e di file e adattabilità. Insieme alle funzionalità di archiviazione a più livelli, a disponibilità elevata e di ripristino di emergenza dell'archiviazione BLOB di Azure.

Per un confronto completo delle opzioni di creazione del cluster con Data Lake Storage Gen2, vedere [confrontare le opzioni di archiviazione per l'uso con i cluster HDInsight di Azure](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Disponibilità di Data Lake Storage Gen2

Data Lake Storage Gen2 è disponibile come opzione di archiviazione per quasi tutti i tipi di cluster HDInsight di Azure sia come predefinito che come account di archiviazione aggiuntivo. HBase, tuttavia, può avere un solo account con Data Lake Storage Gen2.

> [!Note]  
> Dopo aver selezionato Data Lake Storage Gen2 come **tipo di archiviazione primario** , non è possibile selezionare una data Lake storage Gen1 come risorsa di archiviazione aggiuntiva.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Creare cluster HDInsight usando Data Lake Storage Gen2

Usare i collegamenti seguenti per istruzioni dettagliate su come creare cluster HDInsight con accesso a Data Lake Storage Gen2.

* [Uso del portale](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Utilizzare l'interfaccia della riga di comando di Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell non è attualmente supportato per la creazione di un cluster HDInsight con Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controllo di accesso per Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quali tipi di autorizzazioni sono supportati da Data Lake Storage Gen2?

Data Lake Storage Gen2 usa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e gli elenchi di controllo di accesso (ACL) di tipo POSIX. Data Lake Storage Gen1 supporta gli elenchi di controllo di accesso solo per il controllo dell'accesso ai dati.

Il controllo degli accessi in base al ruolo di Azure usa assegnazioni di ruolo per applicare in modo efficace set di autorizzazioni a utenti, gruppi ed entità servizio per le risorse di Azure In genere, le risorse di Azure sono vincolate alle risorse di primo livello, ad esempio gli account di archiviazione BLOB di Azure. Per l'archiviazione BLOB di Azure e Data Lake Storage Gen2 anche questo meccanismo è stato esteso alla risorsa file system.

Per altre informazioni sulle autorizzazioni per i file con RBAC di Azure, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Per altre informazioni sulle autorizzazioni per i file con ACL, vedere [elenchi di controllo di accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Ricerca per categorie controllare l'accesso ai dati in Data Lake Storage Gen2?

La capacità del cluster HDInsight di accedere ai file in Data Lake Storage Gen2 viene controllata tramite identità gestite. Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure. Con le identità gestite non è necessario registrare le entità servizio in Azure AD. Oppure mantenere le credenziali, ad esempio i certificati.

I servizi di Azure hanno due tipi di identità gestite, ovvero assegnati dal sistema e assegnati dall'utente. HDInsight Usa identità gestite assegnate dall'utente per accedere Data Lake Storage Gen2. Un `user-assigned managed identity` viene creato come una risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure.

Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. Per altre informazioni sulle identità gestite, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Ricerca per categorie impostare le autorizzazioni per consentire agli utenti Azure AD di eseguire query sui dati in Data Lake Storage Gen2 usando hive o altri servizi?

Per impostare le autorizzazioni per consentire agli utenti di eseguire query sui dati, utilizzare Azure AD gruppi di sicurezza come entità assegnata negli ACL. Non assegnare direttamente le autorizzazioni di accesso ai file a singoli utenti o entità servizio. Con Azure AD gruppi di sicurezza per controllare il flusso di autorizzazioni, è possibile aggiungere e rimuovere utenti o entità servizio senza riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. È sufficiente aggiungere o rimuovere gli utenti dal gruppo di sicurezza di Azure AD appropriato. Gli ACL non vengono ereditati, pertanto per riapplicare gli ACL è necessario aggiornare l'ACL in ogni file e sottodirectory.

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

È possibile accedere ai file in Data Lake Storage Gen2 da un cluster HDInsight in diversi modi.

* **Uso di nomi completi** . Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Uso del formato con percorso abbreviato** . Con questo approccio si sostituisce il percorso fino alla radice del cluster con:

    ```
    abfs:///<file.path>/
    ```

* **Uso del percorso relativo** . Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono usati tutti e tre gli schemi URI. Sostituire `CONTAINERNAME` e `STORAGEACCOUNT` con i valori appropriati

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi HDFS

1. Creare un file nell'archiviazione locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archiviazione del cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dall'archiviazione locale all'archiviazione del cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archiviazione del cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Creazione di una tabella Hive

Per scopi illustrativi, vengono visualizzati tre percorsi di file. Per l'esecuzione effettiva, usare solo una delle voci `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Passaggi successivi

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integrazione di Azure HDInsight con Data Lake Storage Gen2 (anteprima) - Aggiornamento di ACL e sicurezza)
* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Esercitazione: Estrarre, trasformare e caricare dati usando Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
