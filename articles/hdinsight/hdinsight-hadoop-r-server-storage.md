<properties
   pageTitle="Opzioni di Archiviazione di Azure per R Server in HDInsight Premium | Azure"
   description="Informazioni sulle diverse opzioni di archiviazione disponibili per gli utenti con Server R in HDInsight Premium"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Opzioni di Archiviazione di Azure per R Server in HDInsight Premium

R Server in un cluster HDI ha accesso a BLOB di Azure e presto ad Archivio Azure Data Lake come strumenti di salvataggio permanente di dati, codice, oggetti risultato di analisi, ecc.

Quando si crea un cluster Hadoop in HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è progettato per conservare il file system del cluster creato, ovvero HDFS (Hadoop Distributed File System). Per motivi di prestazioni, il cluster HDInsight viene creato nello stesso data center dell'account di archiviazione primario specificato. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md "Usare l'archiviazione BLOB di Azure con HDInsight").


## Usare più account di archiviazione BLOB di Azure

Se necessario, è possibile accedere a più account di archiviazione di Azure o contenitori con il cluster HDI. A tale scopo è necessario specificare gli account di archiviazione aggiuntivi nell'interfaccia utente al momento della creazione del cluster ed eseguire la procedura seguente per usarli in R.

1.	Si supponga di creare un cluster HDInsight con un nome di account di archiviazione "storage1" con il contenitore predefinito "container1". Si specifica anche un account di archiviazione aggiuntivo "storage2".  
2.	A questo punto si copia un file "mycsv.csv" nella directory "/share" e si desidera eseguire l'analisi sul file.  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	Nel codice R il nodo del nome è stato impostato su "default" e sono stati specificati la directory e il file da elaborare

    myNameNode <- "default" myPort <- 0

  Percorso dei dati

    bigDataDirRoot <- "/share"  

  definire il contesto di calcolo di Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  impostare il contesto di calcolo

    rxSetComputeContext(mySparkCluster)

  definire i file system HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  specificare il file di input in HDFS da analizzare

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
Tutti i riferimenti di file e directory puntano all'account di archiviazione wasb://container1@storage1.blob.core.windows.net poiché si tratta dell'**account di archiviazione predefinito** associato al cluster HDInsight.

Si supponga ora di voler elaborare un file denominato "mySpecial.csv" che si trova nella directory "/private" del contenitore "container2" nel nome di account di archiviazione "storage2".

Nel codice R si modifica il riferimento al nodo del nome nell'account di archiviazione "storage2".

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Percorso dei dati

    bigDataDirRoot <- "/private"

  definire il contesto di calcolo di Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  impostare il contesto di calcolo

    rxSetComputeContext(mySparkCluster)

  definire i file system HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  specificare il file di input in HDFS da analizzare

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
Tutti i riferimenti di file e directory puntano ora all'account di archiviazione wasb://container2@storage2.blob.core.windows.net poiché si tratta del **nodo del nome** specificato.

Si noti che è necessario configurare la directory /user/RevoShare/<SSH username> nell'account di archiviazione "storage2":

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## Usare File di Azure nel nodo perimetrale 

È disponibile anche un'opzione di archiviazione dei dati pratica da usare nel nodo perimetrale denominato [File di Azure](../storage/storage-how-to-use-files-linux.md "File di Azure") che consente di montare una condivisione file dell'archiviazione di Azure nel file system Linux. Questo può essere utile per l'archiviazione di file di dati, script R e oggetti risultato che potrebbero risultare utili in seguito quando sarà opportuno usare il file system nativo nel nodo perimetrale anziché HDFS. Un vantaggio considerevole dell'uso di File di Azure è rappresentato dalla possibilità di montare e usare le condivisioni file da qualsiasi sistema con sistema operativo supportato (Windows o Linux), ad esempio un altro cluster HDInsight personale o di un altro membro del team, una macchina virtuale di Azure e persino un sistema locale.


## Passaggi successivi

Dopo aver visto come creare un nuovo cluster HDInsight che include Server R e le nozioni di base sull'uso della console di R da una sessione SSH, usare le risorse seguenti per trovare altre modalità di utilizzo di Server R in HDInsight.

- [Articolo contenente una panoramica su Server R in Hadoop.](hdinsight-hadoop-r-server-overview.md)
- [Articolo introduttivo relativo a Server R su Hadoop.](hdinsight-hadoop-r-server-get-started.md)
- [Articolo relativo all'installazione di RStudio Server in HDInsight Premium.](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opzioni del contesto di calcolo per R Server in HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0330_2016-->