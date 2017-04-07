---
title: Introduzione all&quot;uso di R Server su HDInsight | Documentazione Microsoft
description: Informazioni sulla creazione di un cluster Apache Spark in HDInsight che include R Server e sull&quot;invio di uno script R nel cluster.
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Introduzione all'uso di R Server su HDInsight
HDInsight include un'opzione R Server da integrare nel cluster HDInsight. In questo modo gli script R possono usare Spark e MapReduce per eseguire i calcoli distribuiti. In questo documento si apprenderà come creare un R Server in un cluster HDInsight e come eseguire uno script R che illustra l'uso di Spark per i calcoli R distribuiti.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**: prima di iniziare questa esercitazione è necessario avere una sottoscrizione di Azure. Per altre informazioni, vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un client Secure Shell (SSH)**: il client SSH viene usato per connettersi da remoto al cluster HDInsight ed eseguire i comandi direttamente nel cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

  * **Chiavi SSH (facoltative)**: è possibile proteggere l'account SSH usato per connettersi al cluster mediante una password o una chiave pubblica. Usare una password è più semplice ed è possibile iniziare senza necessità di creare una coppia di chiavi pubblica/privata. Tuttavia, usare una chiave è più sicuro.

      I passaggi in questo documento presuppongono che si stia usando una password.

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Creare il cluster
> [!NOTE]
> La procedura descritta in questo documento illustrerà come creare un R Server in un cluster HDInsight usando informazioni di configurazione di base. Per altre impostazioni di configurazione del cluster (ad esempio l'uso di account di archiviazione aggiuntivi, di una Rete virtuale di Azure o la creazione di un metastore per Hive), vedere [Creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md). Per creare un R Server con un modello di Azure Resource Manager, vedere [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Distribuire un cluster HDInsight di R Server).
>
>

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **NUOVO**, **Intelligence e analisi** e quindi **HDInsight**.

    ![Immagine della creazione di un nuovo cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Nell'esperienza **Creazione rapida** inserire il nome del cluster nel campo **Nome cluster**. Se sono disponibili più sottoscrizioni di Azure, usare la voce **Sottoscrizione** per selezionare quella da usare.

    ![Selezione del nome del cluster e della sottoscrizione](./media/hdinsight-getting-started-with-r/clustername.png)

4. Selezionare **Tipo di cluster** per aprire il pannello **Configurazione cluster**. Nel pannello **Configurazione cluster** selezionare le opzioni seguenti:

   * **Tipo di cluster**: R Server
   * **Versione**: selezionare la versione di R Server da installare nel cluster. Selezionare l'ultima versione per le funzionalità più recenti. Sono disponibili altre versioni, se necessarie per motivi di compatibilità. Le note sulla versione per ognuna sono disponibili [qui](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **R Studio community edition for R Server**: questo IDE basato su browser viene installato per impostazione predefinita sul nodo perimetrale.  Se si preferisce non installarlo, deselezionare la casella di controllo. Se si sceglie di installarlo, l'URL per l'accesso a RStudio Server sarà disponibile in un pannello delle applicazioni del portale relativo al cluster, dopo la creazione.

   Lasciare le altre opzioni impostate sui valori predefiniti e usare il pulsante **Seleziona** per salvare il tipo di cluster.

   ![Schermata del pannello del tipo di cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Immettere il **nome utente dell'account di accesso del cluster** e la **password dell'account di accesso del cluster**.

   Specificare un **nome utente SSH**.  SSH è usato per connettersi al cluster da remoto tramite un client **Secure Shell (SSH)**. È possibile specificare l'utente SSH in questa finestra di dialogo o dopo aver creato il cluster (scheda Configurazione per il cluster). R Server è configurato in modo da prevedere il **nome utente SSH** "remoteuser".  **Se si usa un nome utente diverso, è necessario eseguire un passaggio aggiuntivo dopo la creazione del cluster.**

   Lasciare selezionata la casella **Usa la stessa password dell'account di accesso del cluster** per usare la **PASSWORD** come tipo di autenticazione a meno che non si preferisca usare una chiave pubblica.  Se si desidera accedere a R Server nel cluster tramite un client remoto, ad esempio RTVS, RStudio o un altro ambiente desktop IDE, è necessario usare una coppia di chiavi pubblica/privata. È necessario optare per la password SSH se si installa RStudio Server Community Edition.     

   Per creare e usare una coppia di chiavi pubblica/privata, deselezionare **Usa la stessa password dell'account di accesso del cluster** e quindi selezionare **CHIAVE PUBBLICA** e procedere come segue.  Queste istruzioni presuppongono che Cygwin con ssh-keygen o equivalente sia già installato.

   * Dal prompt dei comandi sul computer portatile, generare una coppia di chiavi pubblica/privata:

   `ssh-keygen -t rsa -b 2048`

   * Attenersi alla richiesta di un nome di un file di chiave e quindi immettere una passphrase per una maggiore sicurezza. La schermata dovrebbe essere simile a quanto segue:

   ![Riga di comando SSH in Windows](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Verranno creati un file di chiave privata e un file di chiave pubblica con il nome <private-key-filename>.pub, ad esempio furiosa e furiosa.pub.

   ![Dir SSH](./media/hdinsight-getting-started-with-r/dir.png)

   * Specificare quindi il file di chiave pubblica (*.pub) quando si assegnano le credenziali del cluster HDI e infine confermare il gruppo di risorse e l'area e selezionare **Next** (Avanti)

   ![Pannello Credenziali](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Modificare le autorizzazioni per il file di chiave privata sul computer portatile

   `chmod 600 <private-key-filename>`

   * Usare il file di chiave privata con SSH per l'accesso remoto

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   o come parte della definizione del contesto di calcolo di Hadoop Spark per R Server nel client. Vedere Using Microsoft R Server as a Hadoop Client (Usare Microsoft R Server come un client Hadoop) nella sezione [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creazione di un contesto di calcolo per Spark) del documento online [Get started with ScaleR on Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Introduzione a ScaleR in Apache Spark).

6. La creazione rapida esegue la transizione al pannello **Archiviazione** per selezionare le impostazioni dell'account di archiviazione da usare per il percorso principale del file system HDFS usato dal cluster. Selezionare un account di archiviazione di Azure nuovo o esistente oppure un account di archiviazione di Data Lake esistente.

   1. Nel primo caso, scegliere **Selezionare un account di archiviazione** e quindi l'account, oppure creare un nuovo account usando il collegamento **Nuovo** nella sezione **Selezionare un account di archiviazione**.

      > [!NOTE]
      > Se si seleziona **Nuovo** è necessario immettere un nome per il nuovo account di archiviazione. Se il nome viene accettato, verrà visualizzato un segno di spunta verde.

      Il **contenitore predefinito** verrà impostato sul nome predefinito del cluster. Lasciare questo valore.

      Se è stata selezionata l'opzione per un nuovo account di archiviazione, viene visualizzato un prompt che consente di selezionare la **Località** per scegliere l'area in cui creare l'account di archiviazione.  

         ![Pannello di origine dati](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

   2. Se si seleziona l'uso di un Data Lake Store esistente, selezionare l'account di archiviazione ADLS da usare e aggiungere al cluster l'identità ADD del cluster per consentire l'accesso all'archivio. Per altre informazioni su questo processo, vedere [Creare un cluster HDInsight con Data Lake Store tramite il Portale di Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

   Usare il pulsante **Seleziona** per salvare la configurazione dell'origine dati.


7. Verrà quindi visualizzato il pannello **Riepilogo** per convalidare tutte le impostazioni. Qui è possibile modificare le **dimensioni dei Cluster** per cambiare il numero di server del cluster e specificare qualsiasi **azione di script** da eseguire. A meno che non si sia consapevoli di aver bisogno di un cluster di maggiori dimensioni, lasciare il numero di nodi di lavoro sul valore predefinito di `4`. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

   ![riepilogo cluster](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Se necessario, è possibile ridimensionare il cluster in un secondo momento tramite il portale (Cluster -> Impostazioni -> Scala Cluster) per aumentare o ridurre il numero di nodi di lavoro.  Questo può essere utile per nascondere il cluster quando non è in uso o per aggiungere capacità al fine di soddisfare le esigenze delle attività più grandi.
   >
   >

    Ecco alcuni fattori da tenere presente quando si modificano le dimensioni del cluster, dei nodi di dati e del nodo perimetrale:  

   * Quando la quantità di dati è ingente, le prestazioni delle analisi R Server distribuite in Spark sono proporzionali al numero di nodi di lavoro.  

   * Le prestazioni delle analisi Server R sono proporzionali alle dimensioni dei dati analizzati. Ad esempio:  

     * Per quantità di dati di piccole e medie dimensioni, le prestazioni sono migliori se l'analisi avviene in un contesto di calcolo locale sul nodo perimetrale.  Per altre informazioni sugli scenari in cui i contesti di calcolo Spark e locale funzionano meglio, vedere Opzioni del contesto di calcolo per R Server su HDInsight.<br>
     * Se si accede al nodo perimetrale e si esegue lo script R, tutte le funzioni, ad eccezione delle funzioni ScaleR rx, verranno eseguite <strong>localmente</strong> sul nodo perimetrale in modo tale che la memoria e il numero di core del nodo perimetrale vengano ridimensionati secondo le esigenze. Lo stesso vale se si utilizza R Server su HDI come contesto di calcolo remoto dal computer portatile.

     ![Pannello livelli dei prezzi di nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Usare il pulsante **Seleziona** per salvare la configurazione dei piani tariffari del nodo.

   Si noti che è anche presente un collegamento per **Download template and parameters** (Scarica modello e parametri). Facendo clic su questo link verranno visualizzati script utilizzabili per automatizzare la creazione di un cluster con la configurazione selezionata. Questi script sono anche disponibili dalla voce del portale di Azure per il cluster dopo che è stato creato.

   > [!NOTE]
   > La creazione del cluster richiederà del tempo, in genere circa 20 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.
   >
   >

## <a name="connect-to-rstudio-server"></a>Connettersi a RStudio Server

Se si è scelto di includere RStudio Server Community Edition nell'installazione è possibile accedere a RStudio in due modi diversi.

1. Passare all'URL seguente (dove **CLUSTERNAME** è il nome del cluster creato):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. In alternativa aprire la voce relativa al cluster nel portale di Azure, selezionare il collegamento rapido Dashboard R Server e quindi selezionare Dashboard di R Studio:

     ![Accedere al dashboard di R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Accedere al dashboard di R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > A prescindere dal metodo, la prima volta che si esegue l'accesso è necessario autenticarsi due volte.  Alla prima autenticazione, specificare l'ID utente e la password di amministrazione del cluster. Al secondo prompt specificare l'ID utente e la password SSH. Per gli accessi successivi saranno necessari solo l'ID utente e la password SSH.

## <a name="connect-to-the-r-server-edge-node"></a>Connettersi al nodo perimetrale di R Server
Connettersi al nodo perimetrale di R Server del cluster HDInsight tramite SSH:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> È anche possibile trovare l'indirizzo `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` nel portale di Azure selezionando il cluster e quindi **Tutte le impostazioni**, **App** e **RServer**. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.
>
> ![Immagine dell'endpoint SSH per il nodo perimetrale](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Una volta effettuata la connessione, verrà visualizzato un prompt come quello che segue.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>Usare la console di R

1. Nella sessione SSH usare il comando seguente per avviare la console R.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. Dal prompt `>` è possibile immettere codice R. R Server include pacchetti che consentono di interagire facilmente con Hadoop ed eseguire calcoli distribuiti. Ad esempio, usare il comando seguente per visualizzare la root del file system predefinito per il cluster HDInsight.

`rxHadoopListFiles("/")`

È anche possibile usare l'indirizzamento in stile WASB.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Utilizzare R Server in HDI da un'istanza remota di Microsoft R Server o Microsoft R Client
Come indicato nella sezione precedente sull'uso delle coppie di chiavi pubblica/privata per accedere al cluster, è possibile configurare l'accesso al contesto di calcolo Hadoop Spark HDI da un'istanza remota di Microsoft R Server o Microsoft R Client in esecuzione su un computer desktop o portatile. Vedere la sezione Using Microsoft R Server as a Hadoop Client (Usare Microsoft R Server come un client Hadoop) nella sezione [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creazione di un contesto di calcolo per Spark) della [RevoScaleR Hadoop Spark Getting Started guide](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Guida introduttiva di RevoScaleR Hadoop Spark).  A tale scopo è necessario specificare le opzioni seguenti quando si definisce il contesto di calcolo RxSpark sul computer portatile: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Ad esempio:

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>Usare un contesto di calcolo
Un contesto di calcolo consente di controllare se il calcolo verrà eseguito localmente sul nodo perimetrale o se verrà distribuito sui nodi del cluster HDInsight.

1. Da RStudio Server o dalla console di R (in una sessione SSH), usare quanto segue per caricare i dati di esempio nell'archivio predefinito per HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. Successivamente, si creano alcune informazioni sui dati e si definiscono due origini dati in modo da poter lavorare con i dati.

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

3. Si prova poi a eseguire una regressione logistica sui dati usando il contesto di calcolo locale.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    L'output dovrebbe terminare con righe simili alle seguenti.

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

4. Quindi, si prova a eseguire la stessa regressione logistica usando il contesto di Spark. Il contesto di Spark distribuirà l'elaborazione in tutti i nodi di lavoro del cluster HDInsight.

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


   > [!NOTE]
   > È anche possibile usare MapReduce per distribuire il calcolo sui nodi del cluster. Per altre informazioni sul contesto di calcolo, vedere [Opzioni del contesto di calcolo per R Server su HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuire il codice R su più nodi
Con R Server è possibile prelevare facilmente il codice R esistente ed eseguirlo su più nodi del cluster tramite `rxExec`. Questa operazione è utile quando si esegue lo sweep parametrico o le simulazioni. Di seguito è riportato un esempio su come usare `rxExec`.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Se si sta ancora usando il contesto Spark o MapReduce, verrà restituito il valore nodename per i nodi di lavoro su cui è in esecuzione il codice `(Sys.info()["nodename"])`. In un cluster a quattro nodi, ad esempio, è possibile ricevere un output simile al seguente.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Accesso ai dati in Hive e Parquet
Una nuova funzionalità disponibile in R Server 9.0 e versioni successive consente l'accesso diretto ai dati in Hive e Parquet da parte delle funzioni di ScaleR nel contesto di calcolo di Spark. Queste funzionalità sono disponibili tramite nuove funzioni di origine dati ScaleR denominate RxHiveData e RxParquetData, che usano Spark SQL per caricare i dati direttamente in un frame di dati di Spark per l'analisi da parte di ScaleR.  

Di seguito è riportato un codice di esempio per l'uso delle nuove funzioni:



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Per altre informazioni sull'uso di queste funzioni, vedere la guida in linea di R Server tramite i comandi ?RxHiveData e ?RxParquetData.  


## <a name="install-r-packages"></a>Installare pacchetti R
Per installare pacchetti R aggiuntivi nel nodo perimetrale, è possibile usare `install.packages()` direttamente dall'interno della console R quando si è connessi al nodo perimetrale tramite SSH. Tuttavia, se si desidera installare pacchetti R sui nodi di lavoro del cluster, è necessario usare un'azione di script.

Le azioni di script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight oppure per installare software aggiuntivo. In questo caso, viene usato per installare altri pacchetti R. Per installare altri pacchetti tramite un'azione di script, seguire questa procedura.

> [!IMPORTANT]
> Le azioni di script per installare altri pacchetti R sono possono essere usate solo dopo aver creato il cluster. Non è possibile usarle durante la creazione del cluster poiché lo script si basa su R Server completamente installato e configurato.
>
>

1. Nel [Portale di Azure](https://portal.azure.com)selezionare il proprio R Server sul cluster HDInsight.

2. Dal pannello **Impostazioni** selezionare **Azioni script** e quindi **Invia nuova** per inviare una nuova azione di script.

   ![Immagine del pannello Azioni script](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. Immettere le informazioni seguenti dal pannello **Invia azione script**.

   * **Nome**: nome descrittivo per identificare lo script

   * **URI script Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Head**: questa opzione deve essere **deselezionata**

   * **Ruolo di lavoro**: questa opzione deve essere **selezionata**

   * **Nodi perimetrali**: questa opzione deve essere **deselezionata**.

   * **Zookeeper**: questa opzione deve essere **deselezionata**

   * **Parametri**: i pacchetti R da installare. Ad esempio, `bitops stringr arules`

   * **Salvare questa azione script...**: questa opzione deve essere **selezionata**  

   > [!NOTE]
   > 1. Per impostazione predefinita, tutti i pacchetti R vengono installati da uno snapshot dell'archivio MRAN di Microsoft coerente con la versione di R Server che è stato installato.  L'installazione di versioni più recenti dei pacchetti espone al rischio di incompatibilità, tuttavia è possibile eseguire l'operazione specificando `useCRAN` come primo elemento dell'elenco dei pacchetti, ad esempio `useCRAN bitops, stringr, arules`.  
   > 2. Alcuni pacchetti R richiedono librerie di sistema di Linux aggiuntive. Per praticità sono state preinstallate le dipendenze necessarie per i 100 pacchetti R più diffusi. Tuttavia, se i pacchetti R installati richiedono altre librerie, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico su Archiviazione di Azure e usare lo script modificato per installare i pacchetti.
   >    Per altre informazioni sullo sviluppo di azioni script, vedere l'articolo [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Aggiunta di un'azione script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Selezionare **Crea** per eseguire lo script. Una volta completato lo script, i pacchetti R saranno disponibili su tutti i nodi di lavoro.

## <a name="using-microsoft-r-server-operationalization"></a>Uso della messa in funzione di Microsoft R Server
Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni. Per configurare la messa in funzione di Microsoft R Server, seguire la procedura seguente.

Innanzitutto, accedere tramite SSH al nodo perimetrale. Ad esempio, ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

Dopo l'uso di SSH, cambiare directory passando alla seguente ed eseguire sudo sulla dll di dotnet, come illustrato di seguito.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Per configurare la messa in funzione di Microsoft R Server con una configurazione di una casella, procedere come segue.

* Selezionare "1. Configure R Server for Operationalization" (Configurare Server R per la messa in funzione)
* Selezionare "A. One-box (web + compute nodes)” (Una casella (Web + nodi di calcolo)"
* Immettere una password per l'utente **admin**

![Opzione per una casella](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Come passaggio facoltativo è possibile eseguire controlli diagnostici eseguendo un test di diagnostica, come illustrato di seguito.

* Selezionare "6. Run diagnostic tests" (Esegui test diagnostici)
* Selezionare "A. Test configuration" (Configurazione test)
* Immettere nome utente = "admin" e la password del passaggio di configurazione precedente
* Conferma dell'integrità complessiva = pass
* Uscire dall'utilità di amministrazione
* Uscire da SSH

![Diagnostica per la messa in funzione](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

A questo punto la configurazione per la messa in funzione è completata. Ora è possibile utilizzare il pacchetto 'mrsdeploy' su RClient per connettersi alla messa in funzione sul nodo perimetrale e iniziare a usarne le caratteristiche, ad esempio l'[esecuzione remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) e i [servizi Web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). In funzione del fatto che il cluster è impostato su una rete virtuale o meno, potrebbe essere necessario impostare il tunneling di inoltro alla porta tramite l'accesso SSH, come illustrato di seguito:

### <a name="rserver-cluster-on-virtual-network"></a>Cluster RServer su rete virtuale

Assicurarsi di aver consentito il traffico attraverso la porta 12800 verso il nodo perimetrale. In questo modo è possibile usare tale nodo per la connessione alla funzionalità di messa in funzione.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Se remoteLogin() non può connettersi al nodo perimetrale, ma è possibile accedere al nodo tramite SSH, è necessario verificare che la regola che consente il traffico sulla porta 12800 sia stata impostata correttamente. Se il problema continua a sussistere, provare a impostare il tunneling di inoltro alla porta tramite SSH.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster RServer non impostato su rete virtuale

Se il cluster non è impostato sulla rete virtuale oppure se si riscontrano problemi relativi alla connettività sulla rete virtuale, è possibile usare il tunneling di inoltro alla porta con SSH, come indicato di seguito:

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

La configurazione è possibile anche in Putty.

![Connessione SSH con Putty](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Una volta che la sessione SSH è attiva, il traffico proveniente dalla porta 12800 del computer verrà inoltrato alla porta 12800 del nodo perimetrale tramite una sessione SSH. Assicurarsi di usare `127.0.0.1:12800` nel metodo remoteLogin(). Viene così eseguito l'accesso alla messa in funzione del nodo perimetrale tramite l'inoltro alla porta.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Procedura per ridimensionare i nodi di calcolo della messa in funzione di Microsoft R Server nei nodi del ruolo di lavoro di HDInsight


### <a name="decommission-the-worker-nodes"></a>Rimuovere le autorizzazioni dei nodi del ruolo di lavoro
Attualmente Microsoft R Server non è gestito tramite Yarn. Se è necessario rimuovere le autorizzazioni dei nodi dei ruoli di lavoro, il gestore risorse di Yarn non funzionerà come previsto in quanto non sarà in grado di riconoscere le risorse impiegate dal server. Per evitare questo problema, è consigliabile rimuovere le autorizzazioni dei nodi del ruolo di lavoro in cui ridimensionare i nodi di calcolo.

Passaggi per la rimozione delle autorizzazioni dei nodi del ruolo di lavoro:

* Accedere alla console Ambari del cluster HDI e fare clic sulla scheda "Host"
* Selezionare i nodi del ruolo di lavoro di cui rimuovere le autorizzazioni e fare clic su "Actions" (Azioni) > "Selected Hosts" (Host selezionati) > "Host" > e infine su "Turn ON Maintenance Mode" (Attiva modalità di manutenzione). Ad esempio nell'immagine seguente i nodi selezionati per la rimozione delle autorizzazioni sono wn3 e wn4.  

   ![Rimozione delle autorizzazioni dei nodi del ruolo di lavoro](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Selezionare "Actions" > "Selected Hosts" > "DataNodes" > e quindi fare clic su "Decommission" (Rimuovi autorizzazioni).
* Selezionare "Actions" > "Selected Hosts" > "NodeManagers" > e quindi fare clic su "Decommission".
* Selezionare "Actions" > "Selected Hosts" > "DataNodes" > e quindi fare clic su "Stop"
* Selezionare "Actions" > "Selected Hosts" > "NodeManagers" > quindi fare clic su "Stop"
* Selezionare "Actions" > "Selected Hosts" > "Hosts" > e quindi fare clic su "Stop All Components" (Arresta tutti i componenti)
* Deselezionare i nodi del ruolo di lavoro e selezionare i nodi head
* Selezionare "Actions" > "Selected Hosts" > "Hosts" > "Restart All Components"


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurare i nodi di calcolo su ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione

* Accedere tramite SSH a ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione
* Eseguire l'utilità di amministrazione con `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* Immettere "1" per selezionare l'opzione "1. Configure R Server for Operationalization" (Configurare Server R per la messa in funzione)
* Immettere "c" per selezionare l'opzione "C. Compute node" (Nodo di calcolo). Il nodo di calcolo viene configurato sul nodo del ruolo di lavoro.
* Uscire dall'utilità di amministrazione

### <a name="add-compute-nodes-details-on-web-node"></a>Aggiungere i dettagli dei nodi di calcolo nel nodo Web
Una volta configurati tutti i nodi del ruolo di lavoro per i quali è stata rimossa l'autorizzazione affinché vengano eseguiti nel nodo di calcolo, tornare al nodo perimetrale e aggiungere gli indirizzi IP dei nodi del ruolo di lavoro rimossi nella configurazione del nodo Web di Microsoft R Server:

* Accedere tramite SSH al nodo perimetrale
* Eseguire `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`
* Individuare la sezione dell'URI e aggiungere i dettagli relativi alla porta e all'IP del nodo del ruolo di lavoro.

![Rimozione delle autorizzazioni dei nodi del ruolo di lavoro con la riga di comando](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver visto come creare un nuovo cluster HDInsight che include Server R e le nozioni di base sull'uso della console di R da una sessione SSH, usare le risorse seguenti per trovare altre modalità di utilizzo di Server R in HDInsight.

* [Aggiungere RStudio Server a HDInsight (se non installato durante la creazione del cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](hdinsight-hadoop-r-server-storage.md)

