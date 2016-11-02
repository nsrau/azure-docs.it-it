
<properties
   pageTitle="Opzioni di Archiviazione di Azure per R Server su HDInsight (anteprima) | Microsoft Azure"
   description="Informazioni sulle diverse opzioni di archiviazione disponibili per gli utenti con R Server su HDInsight (anteprima)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/> 

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="jeffstok"
/> 

# Opzioni di Archiviazione di Azure per R Server su HDInsight (anteprima)

Microsoft R Server in HDInsight (anteprima) ha accesso sia all'archivio BLOB di Azure sia all'[Archivio Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) come strumenti di salvataggio permanente di dati, codice, oggetti risultato di analisi e così via.

Quando si crea un cluster Hadoop in HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account include il file system del cluster creato, ad esempio Hadoop Distributed File System. Per motivi di prestazioni, il cluster HDInsight viene creato nello stesso data center dell'account di archiviazione primario specificato. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md "Usare l'archiviazione BLOB di Azure con HDInsight").


## Usare più account di archiviazione BLOB di Azure

Se necessario, è possibile accedere a più account di archiviazione o contenitori di Azure con il cluster HDI. A tale scopo è necessario specificare gli account di archiviazione aggiuntivi nell'interfaccia utente al momento della creazione del cluster e quindi seguire questa procedura per usarli in R.

1.	Creare un cluster HDInsight con un nome di account di archiviazione **storage1** con un contenitore predefinito denominato **container1**.
2. Si specifica anche un account di archiviazione aggiuntivo denominato **storage2**.
3. Copiare il file mycsv.csv nella directory /share ed eseguire analisi su tale file.

    ````
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal myscsv.scv /share  
    ````

3.	Nel codice R il nodo del nome è stato impostato su **default** e sono stati specificati la directory e il file da elaborare.

    ````
    myNameNode <- "default"
    myPort <- 0
    ````

  Percorso dei dati:

    bigDataDirRoot <- "/share"  

  Definire il contesto di calcolo di Spark:

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Impostare il contesto di calcolo:

    rxSetComputeContext(mySparkCluster)

  Definire il file system HDFS (Hadoop Distributed File System):

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Specificare il file di input da analizzare in HDFS:

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Tutti i riferimenti a file e directory puntano all'account di archiviazione wasbs://container1@storage1.blob.core.windows.net. Si tratta dell'**account di archiviazione predefinito** associato al cluster HDInsight.

Si supponga ora di voler elaborare un file denominato mySpecial.csv che si trova nella directory /private di **container2** in **storage2**.

Nel codice R puntare il nome del riferimento al nodo sull'account di archiviazione **storage2**.

    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Percorso dei dati:

    bigDataDirRoot <- "/private"

  Definire il contesto di calcolo di Spark:

    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

  Impostare il contesto di calcolo:

    rxSetComputeContext(mySparkCluster)

  Definire i file system HDFS:

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Specificare il file di input da analizzare in HDFS:

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Tutti i riferimenti a file e directory ora puntano all'account di archiviazione wasbs://container2@storage2.blob.core.windows.net. Questo è il **nodo del nome** specificato.

Si noti che è necessario configurare la directory /user/RevoShare/<nomeutente SSH> nell'account di archiviazione **storage2** come indicato di seguito:

    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Usare un Archivio Azure Data Lake

Per usare archivi Data Lake con l'account HDInsight, è necessario concedere al cluster l'accesso a ogni Archivio Azure Data Lake che si vuole usare. Usare l'archivio nello script R come si usa un account di archiviazione secondario, descritto nella procedura precedente.

## Aggiungere l'accesso del cluster agli archivi Azure Data Lake

Per accedere a un Archivio Azure Data Lake, usare un'entità servizio di Azure Active Directory (AAD) associata al cluster HDInsight.

### Per aggiungere un'entità servizio
1. Quando si crea il cluster HDInsight, selezionare **Identità AAD del cluster** dalla scheda **Origine dati**.
2. Nella finestra di dialogo **Identità AAD del cluster** selezionare **Crea nuova** in **Selezionare l'entità servizio di Active Directory**.

Dopo aver assegnato un nome all'entità servizio, creare una password per tale entità. Verrà aperta una nuova scheda in cui è possibile associare l'entità servizio agli archivi Data Lake.

Si noti che è anche possibile aggiungere l'accesso a un Azure Data Lake Store aprendo Azure Data Lake Store nel portale di Azure e passando a **Esplora dati** > **Accesso**. Di seguito è riportato un esempio di finestra di dialogo che mostra come creare un'entità servizio e associarla all'Archivio Data Lake "rkadl11".

![Creare un'entità servizio di archiviazione 1 dell'Archivio Data Lake](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Creare un'entità servizio di archiviazione 2 dell'Archivio Data Lake](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Usare un Archivio Azure Data Lake con R Server
Una volta che è stato concesso l'accesso a un Archivio Data Lake, è possibile usarlo in R Server in HDInsight come con un account di archiviazione di Azure secondario. L'unica differenza è che il prefisso **wasb://** cambia in **adl://** come indicato di seguito:

````
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
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Ecco i comandi usati per configurare l'account di archiviazione di Data Lake con la directory RevoShare e aggiungere il file CSV di esempio dall'esempio precedente:

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Usare File di Azure nel nodo perimetrale

È disponibile anche una pratica opzione di archiviazione dei dati da usare nel nodo perimetrale denominata [File di Azure](../storage/storage-how-to-use-files-linux.md "File di Azure"). Consente di montare una condivisione file di Archiviazione di Azure nel file system Linux. Questo approccio può essere utile per l'archiviazione di file di dati, script R e oggetti risultato che potrebbero essere necessari in seguito, quando sarà opportuno usare il file system nativo nel nodo perimetrale invece di HDFS.

Un vantaggio importante di File di Azure riguarda la possibilità di montare e usare le condivisioni file in qualsiasi sistema dotato di un sistema operativo supportato, ad esempio Windows o Linux. Ad esempio, può essere usato da un altro cluster HDInsight disponibile all'utente o a un membro del team, da una macchina virtuale di Azure o anche da un sistema locale.


## Passaggi successivi

Dopo avere appreso le nozioni di base sull'uso della console di R da una sessione SSH e come creare un nuovo cluster HDInsight che include R Server. usare i collegamenti seguenti per trovare altre modalità di utilizzo di R Server in HDInsight.

- [Panoramica: R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-overview.md)
- [Articolo introduttivo relativo a Server R su Hadoop.](hdinsight-hadoop-r-server-get-started.md)
- [Articolo relativo all'installazione di RStudio Server in HDInsight Premium.](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0921_2016-->