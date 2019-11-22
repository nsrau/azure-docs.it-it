---
title: 'Guida introduttiva: Script R in ML Services e console R - Azure HDInsight'
description: In questa guida introduttiva si eseguirà uno script R in un cluster ML Services in Azure HDInsight con la console R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: af41a7569dedc9a56f67be8ae791d7185e0c7489
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241592"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Guida introduttiva: Eseguire uno script R in un cluster ML Services in Azure HDInsight con la console R

ML Services in Azure HDInsight consente agli script R di usare Apache Spark e Apache Hadoop MapReduce per eseguire i calcoli distribuiti. ML Services controlla l'esecuzione delle chiamate impostando il contesto di calcolo. Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire le funzioni distribuite parallelizzate di RevoScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Apache Spark di RevoScaleR.

Questa guida introduttiva illustra come eseguire uno script R con una console R che mostra l'uso di Spark per i calcoli R distribuiti. Si definirà un contesto di calcolo per l'esecuzione di calcoli in locale su un nodo perimetrale e nuovamente distribuiti tra i nodi del cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster ML Services in HDInsight. Consultare [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **ML Services** per **Tipo di cluster**.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Connessione a una console R

1. Connettersi al nodo perimetrale di un cluster ML Services HDInsight tramite SSH. Modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Nella sessione SSH usare il comando seguente per avviare la console R:

    ```
    R
    ```

    Verrà visualizzato un output con la versione di ML Services e altre informazioni.


## <a name="use-a-compute-context"></a>Usare un contesto di calcolo

1. Dal prompt `>` è possibile immettere codice R. Usare il codice seguente per caricare i dati di esempio nell'archivio predefinito per HDInsight:

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Il completamento di questo passaggio potrebbe richiedere circa 10 minuti.

1. Creare alcune informazioni sui dati e definire due origini dati. Immettere il codice seguente nella console R:

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Eseguire una regressione logistica sui dati usando il contesto di calcolo **locale**. Immettere il codice seguente nella console R:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    I calcoli dovrebbero richiedere circa 7 minuti. L'output dovrebbe terminare con righe simili al frammento seguente:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Eseguire la stessa regressione logistica usando il contesto di **Spark**. Il contesto di Spark distribuisce l'elaborazione su tutti i nodi del ruolo di lavoro del cluster HDInsight. Immettere il codice seguente nella console R:

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    I calcoli dovrebbero richiedere circa 5 minuti.

1. Per chiudere la console di R, usare il comando seguente:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come eseguire uno script R con una console R che mostra l'uso di Spark per i calcoli R distribuiti.  Passare all'articolo successivo per scoprire le opzioni disponibili per specificare se e come l'esecuzione venga parallelizzata tra i core del nodo perimetrale o del cluster HDInsight.

> [!div class="nextstepaction"]
>[Opzioni del contesto di calcolo per ML Services in HDInsight](./r-server-compute-contexts.md)