---
title: 'Esercitazione: Usare R nel contesto di calcolo Spark in Azure HDInsight'
description: 'Esercitazione: introduzione a R e Spark in un cluster di Machine Learning Services per Azure HDInsight.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227436"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Esercitazione: Usare R nel contesto di calcolo Spark in Azure HDInsight

Questa esercitazione include un'introduzione dettagliata all'uso delle funzioni R in Apache Spark in esecuzione in un cluster di Machine Learning Services per Azure HDInsight.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Scaricare i dati di esempio nella risorsa di archiviazione locale
> * Copiare i dati nella risorsa di archiviazione predefinita
> * Configurare un set di dati
> * Creare origini dati
> * Creare un contesto di calcolo per Spark
> * Adattare un modello lineare
> * Usare file XDF compositi
> * Convertire i file XDF in CSV

## <a name="prerequisites"></a>Prerequisiti

* Un cluster di Machine Learning Services per Azure HDInsight. Passare a [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e per **Tipo di cluster** selezionare **ML Services**.

## <a name="connect-to-rstudio-server"></a>Connettersi a RStudio Server

RStudio Server viene eseguito nel nodo perimetrale del cluster. Passare al sito seguente (in cui *CLUSTERNAME* nell'URL è il nome del cluster di Machine Learning Services per HDInsight creato):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

La prima volta che si esegue l'accesso, autenticarsi due volte. Alla prima richiesta di autenticazione specificare il nome utente e la password di amministratore del cluster (il valore predefinito è *admin*). Alla seconda richiesta di autenticazione specificare il nome utente e la password SSH (il valore predefinito è *sshuser*). Per gli accessi successivi saranno necessarie solo le credenziali SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Scaricare i dati di esempio nella risorsa di archiviazione locale

Il *set di dati Airline 2012 On-Time* è costituito da 12 file con valori delimitati da virgole contenenti informazioni su arrivi e partenze di tutti i voli commerciali all'interno degli USA per l'anno 2012. Questo set di dati è di grandi dimensioni, con più di 6 milioni di osservazioni.

1. Inizializzare alcune variabili di ambiente. Nella console di RStudio Server immettere il codice seguente:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Nel riquadro a destra, selezionare la scheda **Ambiente**. Le variabili vengono visualizzate in **Valori**.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Creare una directory locale e scaricare i dati di esempio. In RStudio, immettere il codice seguente:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Il download dovrebbe essere completato in circa 9,5 minuti.

## <a name="copy-the-data-to-default-storage"></a>Copiare i dati nella risorsa di archiviazione predefinita

La posizione di HDFS (Hadoop Distributed File System) è specificata con la variabile `airDataDir`. In RStudio, immettere il codice seguente:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Il passaggio dovrebbe essere completato in circa 10 secondi.

## <a name="set-up-a-dataset"></a>Configurare un set di dati

1. Creare un oggetto file system che usa i valori predefiniti. In RStudio, immettere il codice seguente:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Dal momento che i file CSV originali hanno nomi di variabili piuttosto complessi, specificare un elenco *colInfo* per renderli più gestibili. In RStudio, immettere il codice seguente:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Creare origini dati

In un contesto di calcolo Spark è possibile creare origini dati con le funzioni seguenti:

|Funzione | DESCRIZIONE |
|---------|-------------|
|`RxTextData` | Un'origine dati di testo delimitato da virgole. |
|`RxXdfData` | Dati in formato di file XDF. In RevoScaleR il formato di file XDF è modificato per Hadoop per archiviare dati in un set di file compositi invece che in un singolo file. |
|`RxHiveData` | Genera un oggetto origine dati Hive.|
|`RxParquetData` | Genera un oggetto origine dati Parquet.|
|`RxOrcData` | Genera un oggetto origine dati Orc.|

Creare un oggetto [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) usando i file copiati in HDFS. In RStudio, immettere il codice seguente:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Creare un contesto di calcolo per Spark

Per caricare i dati ed eseguire analisi sui nodi di lavoro, impostare il contesto di calcolo nello script su [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). In questo contesto le funzioni R distribuiscono automaticamente il carico di lavoro tra tutti i nodi di lavoro, senza requisiti predefiniti per la gestione dei processi o della coda. Il contesto di calcolo Spark viene stabilito tramite `RxSpark` o `rxSparkConnect()` e usa `rxSparkDisconnect()` per tornare in un contesto di calcolo locale. In RStudio, immettere il codice seguente:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Adattare un modello lineare

1. Usare la funzione [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) per adattare un modello lineare con l'origine dati `airDS`. In RStudio, immettere il codice seguente:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Questo passaggio dovrebbe essere completato in circa 2-3 minuti.

1. Visualizzare i risultati. In RStudio, immettere il codice seguente:

    ```R
    summary(delayArr)
    ```

    I risultati visualizzati sono simili ai seguenti:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    I risultati indicano che sono stati elaborati tutti i dati, sei milioni di osservazioni, usando tutti i file CSV nella directory specificata. Dal momento che è stato specificato `cube = TRUE`, è disponibile un coefficiente stimato per ogni giorno della settimana (e non l'intercetta).

## <a name="use-composite-xdf-files"></a>Usare file XDF compositi

Come si è visto, è possibile analizzare i file CSV direttamente con R in Hadoop. È tuttavia possibile eseguire l'analisi più rapidamente se i dati vengono archiviati in un formato più efficiente. Il formato file XDF di R è efficiente, ma viene leggermente modificato per HDFS in modo che i singoli file rimangano all'interno di un singolo blocco HDFS. Le dimensioni del blocco HDFS variano da un'installazione all'altra, ma sono in genere di 64 MB o 128 MB. 

Quando si usa [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) in Hadoop per creare un set di file XDF compositi, si specifica un'origine dati `RxTextData`, ad esempio `AirDS`, come inData e un'origine dati `RxXdfData` con fileSystem impostato su un file system HDFS come argomento outFile. Sarà quindi possibile usare l'oggetto `RxXdfData` come argomento di dati nelle successive analisi di R.

1. Definire un oggetto `RxXdfData`. In RStudio, immettere il codice seguente:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Impostare le dimensioni del blocco su 250000 righe e specificare che vengono letti tutti i dati. In RStudio, immettere il codice seguente:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importare i dati con `rxImport`. In RStudio, immettere il codice seguente:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Questo passaggio dovrebbe essere completato in pochi minuti.

1. Ripetere la stima dello stesso modello lineare, usando la nuova origine dati più veloce. In RStudio, immettere il codice seguente:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Questo passaggio dovrebbe essere completato in meno di un minuto.

1. Visualizzare i risultati. I risultati dovrebbero essere uguali a quelli dei file CSV. In RStudio, immettere il codice seguente:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Convertire i file XDF in CSV

### <a name="in-a-spark-context"></a>In un contesto Spark

Se i file CSV sono stati convertiti in XDF per maggiore efficienza durante l'esecuzione di analisi, ma si vogliono ora convertire i dati di nuovo in CSV, è possibile usare [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Per creare una cartella di file CSV, creare prima un oggetto `RxTextData` con un nome di directory come argomento file. Questo oggetto rappresenta la cartella in cui creare i file CSV. La directory viene creata quando si esegue `rxDataStep`. Quindi, puntare a questo oggetto `RxTextData` nell'argomento `outFile` di `rxDataStep`. A ogni file CSV creato viene assegnato il nome della directory seguito da un numero.

Si supponga di voler scrivere una cartella di CSV in HDFS dai file XDF compositi `airDataXdf` dopo aver eseguito la regressione logistica e la stima, in modo che i nuovi file CSV contengano i valori stimati e quelli residui. In RStudio, immettere il codice seguente:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Questo passaggio dovrebbe essere completato in circa 2,5 minuti.

`rxDataStep` ha scritto un file CSV per ogni file con estensione XDFD nel file XDF composito di input. Questo è il comportamento predefinito della scrittura di file CSV da file XDF compositi in HDFS quando il contesto di calcolo è impostato su `RxSpark`.

### <a name="in-a-local-context"></a>In un contesto locale

In alternativa, dopo aver completato le analisi è possibile reimpostare il contesto di calcolo su `local` e sfruttare due argomenti all'interno di `RxTextData` che offrono un controllo leggermente maggiore per la scrittura di file CSV in HDFS: `createFileSet` e `rowsPerOutFile`. Se `createFileSet` è impostato su `TRUE`, viene scritta una cartella di file CSV nella directory specificata dall'utente. Se `createFileSet` è impostato su `FALSE`, viene scritto un singolo file CSV. È possibile impostare il secondo argomento, `rowsPerOutFile`, su un numero intero per indicare quante righe scrivere in ogni file CSV quando `createFileSet` è `TRUE`.

In RStudio, immettere il codice seguente:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Questo passaggio dovrebbe essere completato in circa 10 minuti.

Quando si usa un contesto di calcolo `RxSpark`, per impostazione predefinita `createFileSet` è impostato su `TRUE` e `rowsPerOutFile` non ha effetto. DI conseguenza, se si vuole creare un singolo file CSV o personalizzare il numero di righe per file, eseguire `rxDataStep` in un contesto di calcolo `local` (i dati possono comunque essere in HDFS).

## <a name="final-steps"></a>Passaggi finali

1. Eseguire la pulizia dei dati. In RStudio, immettere il codice seguente:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Arrestare l'applicazione Spark remota. In RStudio, immettere il codice seguente:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Uscire dalla sessione R. In RStudio, immettere il codice seguente:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzioni R in Apache Spark in esecuzione in un cluster di Machine Learning Services per HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Opzioni del contesto di calcolo per un cluster di Machine Learning Services per Azure HDInsight](r-server-compute-contexts.md)
* [Funzioni R per Spark in Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
