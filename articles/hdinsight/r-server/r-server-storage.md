---
title: Soluzioni di Archiviazione di Azure per ML Services in HDInsight - Azure
description: Informazioni sulle diverse opzioni di archiviazione disponibili per gli utenti con ML Services in HDInsight
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 4c3b36891030ae97f382d5e1d0623fa7c698dbf6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395088"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluzioni di Archiviazione di Azure per ML Services in Azure HDInsight

ML Services in HDInsight può usare un'ampia gamma di soluzioni di archiviazione per rendere persistenti i dati, i codici o gli oggetti che contengono risultati di analisi. Di seguito sono indicate alcune opzioni disponibili:

- [BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Archiviazione di Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [Archiviazione file di Azure](https://azure.microsoft.com/services/storage/files/)

È possibile anche accedere a più account di archiviazione o contenitori di Azure con il cluster HDInsight. Archiviazione di File di Azure è un'opzione di archiviazione di dati ideale per l'uso nel nodo perimetrale che consente di montare ad esempio una condivisione di file di Archiviazione di Azure sul file system di Linux. Tuttavia, le condivisioni File di Azure possono essere montate e usate in qualsiasi sistema dotato di un sistema operativo supportato, ad esempio Windows o Linux. 

Quando si crea un cluster Hadoop in HDInsight, si specifica un account di **Archiviazione di Azure** o un **archivio Data Lake**. Un contenitore di archiviazione specifico dell'account include il file system del cluster creato, ad esempio Hadoop Distributed File System. Per altre informazioni e istruzioni, vedere:

- [Usare l'Archiviazione di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Usare Data Lake Store con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Usare gli account di archiviazione BLOB di Azure con il cluster ML Services

Se durante la creazione del cluster ML Services è stato specificato più di un account di archiviazione, le istruzioni seguenti illustrano come usare un account secondario per l'accesso ai dati e le operazioni nel cluster ML Services. Si suppongano il contenitore e gli account di archiviazione seguenti: **storage1**, un contenitore predefinito denominato **container1** e **storage2** con **container2**.

> [!WARNING]
> Per motivi di prestazioni, il cluster HDInsight viene creato nello stesso data center dell'account di archiviazione primario specificato. L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Usare l'archiviazione predefinita con ML Services in HDInsight

1. Usando un client SSH, connettersi al nodo perimetrale del cluster. Per altre informazioni sull'uso di SSH con cluster HDInsight, vedere l'articolo [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copiare un file di esempio, mysamplefile.csv, nella directory /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Passare a R Studio o un'altra console R e scrivere il codice R per impostare il nodo dei nomi su **default** e il percorso del file a cui si vuole accedere.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Tutti i riferimenti a file e directory puntano all'account di archiviazione `wasb://container1@storage1.blob.core.windows.net`. Si tratta dell'**account di archiviazione predefinito** associato al cluster HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Usare l'archiviazione aggiuntiva con ML Services in HDInsight

Si supponga ora di voler elaborare un file denominato mysamplefile1.csv, che si trova nella directory /private di **container2** in **storage2**.

Nel codice R puntare il nome del riferimento al nodo sull'account di archiviazione **storage2** .

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Tutti i riferimenti a file e directory ora puntano all'account di archiviazione `wasb://container2@storage2.blob.core.windows.net`. Questo è il **nodo del nome** specificato.

È necessario configurare la directory /user/RevoShare/<SSH username> in **storage2** come indicato di seguito:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-ml-services-cluster"></a>Usare Azure Data Lake Store con il cluster ML Services 

Per usare Data Lake Store con il cluster HDInsight è necessario concedere al cluster l'accesso a ogni Azure Data Lake Store da usare. Per istruzioni su come usare il portale di Azure per creare un cluster HDInsight con Azure Data Lake Store come risorsa di archiviazione predefinita o archivio aggiuntivo, vedere [Create an HDInsight cluster with Data Lake Store using Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure).

Usare quindi l'archivio nello script R come si usa un account di archiviazione Azure secondario, descritto nella procedura precedente.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Aggiungere l'accesso del cluster a Azure Data Lake Store
Per accedere a un Archivio Azure Data Lake, usare un'entità servizio di Azure Active Directory (AAD) associata al cluster HDInsight.

1. Quando si crea il cluster HDInsight, selezionare **Identità AAD del cluster** nella scheda **Origine dati**.

2. Nella finestra di dialogo **Identità AAD del cluster** selezionare **Crea nuova** in **Selezionare l'entità servizio di Active Directory**.

Dopo aver assegnato un nome all'entità servizio, creare una password per tale entità e fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake** per associare l'entità servizio al Data Lake Store.

Dopo la creazione del cluster è anche possibile aggiungere l'accesso tramite cluster a uno o più account Data Lake Store. Aprire la voce portale di Azure per un Data Lake Store e passare a **Esplora dati > Accesso > Aggiungi**. 

### <a name="how-to-access-the-data-lake-store-from-ml-services-on-hdinsight"></a>Come accedere a Data Lake Store da ML Services in HDInsight

Dopo che è stato concesso l'accesso a Data Lake Store, è possibile usarlo nel cluster ML Services in HDInsight come con un account di archiviazione di Azure secondario. L'unica differenza è che il prefisso **wasb://** cambia in **adl://** come indicato di seguito:


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

I comandi seguenti sono usati per configurare l'account di Data Lake Store con la directory RevoShare e aggiungere il file CSV di esempio dall'esempio precedente:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Usare Archiviazione file di Azure con ML Services in HDInsight

È disponibile anche una pratica opzione di archiviazione dei dati da usare nel nodo perimetrale, denominata [File di Azure](https://azure.microsoft.com/services/storage/files/). Consente di montare una condivisione file di Archiviazione di Azure nel file system Linux. Questa opzione può essere utile per l'archiviazione di file di dati, script R e oggetti risultato che potrebbero essere necessari in seguito, soprattutto quando sarà opportuno usare il file system nativo nel nodo perimetrale invece di HDFS. 

Un vantaggio importante di File di Azure riguarda la possibilità di montare e usare le condivisioni file in qualsiasi sistema dotato di un sistema operativo supportato, ad esempio Windows o Linux. Ad esempio, può essere usato da un altro cluster HDInsight disponibile all'utente o a un membro del team, da una macchina virtuale di Azure o anche da un sistema locale. Per altre informazioni, vedere:

- [Come usare Archiviazione file di Azure con Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Come usare Archiviazione file di Azure su Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del cluster ML Services in HDInsight](r-server-overview.md)
* [Introduzione all'uso di un cluster ML Services su Hadoop](r-server-get-started.md)
* [Opzioni del contesto di calcolo per il cluster ML Services in HDInsight](r-server-compute-contexts.md)

