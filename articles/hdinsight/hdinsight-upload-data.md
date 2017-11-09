---
title: Caricare dati per processi Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come caricare i dati per processi Hadoop e accedervi in HDInsight con Interfaccia della riga di comando di Azure, Azure Storage Explorer, Azure PowerShell, la riga di comando di Hadoop o Sqoop.
keywords: hadoop etl, recupero dati in hadoop, caricare dati in hadoop
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 9cdf41bd5c15abca3570a08584dc0958497848a7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Caricare dati per processi Hadoop in HDInsight

Azure HDInsight include un file system HDFS (Hadoop Distributed File System) completo su Archiviazione di Microsoft Azure e Azure Data Lake Store. Archiviazione di Azure e Data Lake Store sono stati progettati come estensione Hadoop Distributed File System per offrire un'esperienza lineare ai clienti. Abilitano il set completo di componenti nell'ecosistema Hadoop, con possibilità di agire direttamente sui dati gestiti da Hadoop stesso. Archiviazione di Azure e Data Lake Store sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per informazioni sui vantaggi dell'uso di Archiviazione di Microsoft Azure, vedere [Usare Archiviazione di Microsoft Azure con HDInsight][hdinsight-storage] e [Usare Data Lake Store con HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, tenere presenti i requisiti seguenti:

* Disporre di un cluster HDInsight di Azure. Per istruzioni vedere [Introduzione ad Azure HDInsight][hdinsight-get-started] o [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conoscenza dei due articoli seguenti:

    - [Usare l'Archiviazione di Azure con HDInsight][hdinsight-storage]
    - [Usare Data Lake Store con HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Caricare i dati in Archiviazione di Azure

### <a name="command-line-utilities"></a>Utilità della riga di comando
Microsoft fornisce le utilità seguenti da usare con Archiviazione di Azure:

| Strumento | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interfaccia della riga di comando di Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Comando Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Mentre l'interfaccia della riga di comando di Azure, Azure PowerShell e AzCopy possono tutti essere usati dall'esterno di Azure, il comando Hadoop è disponibile solo nei cluster HDInsight. Il comando consente solo il caricamento dei dati dal file system locale in Archiviazione di Azure.
>
>

#### <a id="xplatcli"></a>
L'interfaccia della riga di comando di Azure è uno strumento multipiattaforma che consente di gestire i servizi di Azure. Per caricare i dati in Archiviazione di Azure, seguire questa procedura:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installare e configurare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows](../cli-install-nodejs.md).
2. Aprire un prompt dei comandi, una sessione Bash o un'altra shell e usare quanto riportato di seguito per eseguire l'autenticazione alla sottoscrizione di Azure.

    ```cli
    azure login
    ```

    Quando richiesto, immettere il nome utente e la password per la sottoscrizione.
3. Immettere il comando seguente per elencare gli account di archiviazione per la sottoscrizione:

    ```cli
    azure storage account list
    ```

4. Selezionare l'account di archiviazione contenente il BLOB che si desidera usare, quindi usare il comando seguente per recuperare la chiave per tale account:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Questo comando restituisce le chiavi **primaria** e **secondaria**. Copiare il valore della chiave **Primary** poiché varrà usato nei passaggi successivi.
5. Usare il comando seguente per recuperare un elenco di contenitori BLOB all'interno dell'account di archiviazione:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Per caricare e scaricare i file dal BLOB, usare i comandi seguenti:

   * Per caricare un file:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Per scaricare un file:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Se si usa sempre lo stesso account di archiviazione, anziché specificare l'account e la chiave per ogni comando è possibile impostare le variabili di ambiente seguenti:
>
> * **AZURE\_STORAGE\_ACCOUNT**: nome dell'account di archiviazione
> * **AZURE\_STORAGE\_ACCESS\_KEY**: chiave dell'account di archiviazione
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per informazioni sulla configurazione della workstation per l'esecuzione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Per caricare un file locale in Archiviazione di Azure**

1. Aprire la console di Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
2. Impostare i valori delle prime cinque variabili nello script seguente:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Incollare lo script nella console di Azure PowerShell per eseguirlo e copiare il file.

Per script di PowerShell di esempio creati per funzionare con HDInsight, vedere [Strumenti HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy è un strumento della riga di comando progettato per semplificare l'attività di trasferimento dati in entrata e in uscita da un account di archiviazione di Azure. Può essere usato come strumento autonomo o può essere incorporato in un'applicazione esistente. [Download di AzCopy][azure-azcopy-download].

La sintassi di AzCopy è:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Per altre informazioni, vedere [AzCopy: caricamento/download di file per BLOB di Azure][azure-azcopy].

È disponibile l'anteprima di Azcopy in Linux.  Vedere [Annuncio della versione di anteprima di AzCopy in Linux](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Riga di comando di Hadoop
La riga di comando di Hadoop è utile solo per archiviare i dati nel BLOB di archiviazione di Azure quando i dati sono già presenti nel nodo head del cluster.

Per usare il comando Hadoop, è innanzitutto necessario connettersi nel nodo head mediante uno dei metodi seguenti:

* **HDInsight basato su Windows**: [connettersi tramite Desktop remoto](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight basato su Linux**: connettersi tramite [SSH o PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo essersi connessi, è possibile usare la sintassi seguente per caricare un file nell'archiviazione.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Ad esempio, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Poiché il file system predefinito per HDInsight si trova in Archiviazione di Microsoft Azure, /example/datadavinci.txt si trova effettivamente in Archiviazione di Microsoft Azure. È inoltre possibile fare riferimento al file come segue:

    wasb:///example/data/data.txt

oppure

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Per un elenco di altri comandi Hadoop che funzionano con i file, vedere [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> Nei cluster HBase la dimensione di blocco predefinita usata per la scrittura dei dati è 256 KB. Questa impostazione non costituisce un problema quando si usano API HBase o REST, ma l'uso dei comandi `hadoop` o `hdfs dfs` per scrivere dati di dimensioni superiori a ~12 GB provoca un errore. Per altre informazioni, vedere la sezione [Eccezione di archiviazione per la scrittura nel BLOB](#storageexception) più avanti in questo articolo.
>
>

### <a name="graphical-clients"></a>Client con interfaccia grafica
Esistono diverse applicazioni che forniscono un'interfaccia grafica per usare Archiviazione di Azure. Nella tabella seguente è riportato un elenco di alcune di queste applicazioni:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools per HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools per HDInsight
Per altre informazioni, vedere [Esplorare le risorse collegate](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

#### <a id="storageexplorer"></a>Azure Storage Explorer
*Azure Storage Explorer* è uno strumento utile per l'esame e la modifica dei dati nei BLOB. Si tratta di uno strumento gratuito open-source che è possibile scaricare da [http://storageexplorer.com/](http://storageexplorer.com/). Anche il codice sorgente è disponibile da questo collegamento.

Prima di usare lo strumento è necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni sull'acquisizione di queste informazioni, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" dell'articolo [Creare, gestire o eliminare un account di archiviazione][azure-create-storage-account].

1. Eseguire Azure Storage Explorer. Se è la prima volta che si esegue Storage Explorer, è necessario specificare il **_nome account di archiviazione** e la **chiave account di archiviazione**. Se questo strumento è stato eseguito prima, usare il pulsante **Aggiungi** per aggiungere un nuovo nome e una nuova chiave dell'account di archiviazione.

    Immettere il nome e la chiave dell'account di archiviazione usati dal cluster HDInsight e quindi selezionare **SALVA E APRI**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Nell'elenco dei contenitori a sinistra dell'interfaccia, fare clic sul nome del contenitore associato al cluster HDInsight. Per impostazione predefinita, questo è il nome del cluster HDInsight, ma potrebbe essere diverso se è stato immesso un nome specifico durante la creazione del cluster.
3. Sulla barra degli strumenti, selezionare l'icona di caricamento.

    ![Barra degli strumenti con icona di caricamento evidenziata](./media/hdinsight-upload-data/toolbar.png)
4. Specificare un file da caricare e quindi fare clic su **Open**. Quando richiesto, selezionare **Carica** per caricare il file nella directory principale del contenitore di archiviazione. Se si vuole caricare il file in un percorso specifico, immettere il percorso nel campo **Destinazione**, quindi selezionare **Carica**.

    ![Finestra di dialogo di caricamento file](./media/hdinsight-upload-data/fileupload.png)

    Dopo che il file ha terminato il caricamento, è possibile utilizzarlo dai processi nel cluster HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Montare Archiviazione di Azure come unità locale
Vedere [Montare Archiviazione di Azure come unità locale](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Caricamento tramite servizi
#### <a name="azure-data-factory"></a>Data factory di Azure
Data factory di Azure è un servizio completamente gestito per la composizione di servizi di archiviazione, elaborazione e spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili e affidabili.

Azure Data Factory può essere usato per spostare i dati in Archiviazione di Microsoft Azure o per creare pipeline di dati che usano direttamente le funzionalità di HDInsight, ad esempio Hive e Pig.

Per altre informazioni, vedere [Documentazione di Data factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS.

Per altre informazioni, vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDK di sviluppo
È possibile accedere ad Archiviazione di Microsoft Azure anche tramite un SDK di Azure dai linguaggi di programmazione seguenti:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Per altre informazioni sull'installazione di SDK di Azure, vedere [Download di Azure](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Risoluzione dei problemi
#### <a id="storageexception"></a>Eccezione di archiviazione per la scrittura nel BLOB
**Sintomi**: quando si usa il comando `hadoop` o `hdfs dfs` per scrivere file di dimensioni pari o superiori a ~12 GB in un cluster HBase, è possibile che si verifichi l'errore seguente:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: i cluster HBase in HDInsight usano per impostazione predefinita dimensioni di blocco pari a 256 KB per la scrittura nelle risorse di archiviazione di Azure. Benché questa impostazione sia ottimale per le API HBase o REST, genera un errore se si usano le utilità da riga di comando `hadoop` o `hdfs dfs`.

**Risoluzione**: usare `fs.azure.write.request.size` per specificare dimensioni maggiori per i blocchi. È possibile eseguire questa operazione per i singoli utenti usando il parametro `-D`. Il comando seguente è un esempio di utilizzo di questo parametro con il comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

È anche possibile aumentare globalmente il valore di `fs.azure.write.request.size` usando Ambari. La procedura seguente consente di cambiare il valore nell'interfaccia utente Web di Ambari:

1. Nel browser passare all'interfaccia utente Web di Ambari per il cluster, ovvero https://CLUSTERNAME.azurehdinsight.net, dove **CLUSTERNAME** è il nome del cluster.

    Quando richiesto, immettere il nome dell'amministratore e la password per il cluster.
2. Sul lato sinistro dello schermo selezionale **HDFS** e quindi fare clic sulla scheda **Configs** (Configurazioni).
3. Nel campo **Filter** (Filtro) immettere `fs.azure.write.request.size`. Il campo e il valore corrente vengono visualizzati al centro della pagina.
4. Modificare il valore da 262144 (256 KB) al nuovo valore, ad esempio 4194304 (4 MB).

![Immagine della modifica del valore tramite l'interfaccia utente Web di Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso come importare dati in HDInsight, leggere gli articoli seguenti per informazioni sull'esecuzione di analisi:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
