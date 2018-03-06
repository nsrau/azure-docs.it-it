---
title: Introduzione all'uso di R Server in HDInsight - Azure | Microsoft Docs
description: Informazioni sulla creazione di un cluster Apache Spark in HDInsight che include R Server e sull'invio di uno script R nel cluster.
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Introduzione a R Server in HDInsight

Azure HDInsight include un'opzione R Server da integrare nel cluster HDInsight. Con questa opzione, gli script R possono usare Spark e MapReduce per eseguire i calcoli distribuiti. Questo articolo descrive come creare un'istanza di R Server in un cluster HDInsight. Verrà quindi illustrato come eseguire uno script R che mostra l'uso di Spark per i calcoli R distribuiti.


## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**: prima di iniziare questa esercitazione è necessario avere una sottoscrizione di Azure. Per altre informazioni, vedere il video su come [ottenere una versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un client Secure Shell (SSH)**: il client SSH viene usato per connettersi da remoto al cluster HDInsight ed eseguire i comandi direttamente nel cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Chiavi SSH (facoltative)**: è possibile proteggere l'account SSH usato per connettersi al cluster mediante una password o una chiave pubblica. Usare una password è più semplice ed è possibile iniziare senza necessità di creare una coppia di chiavi pubblica/privata. Tuttavia, usare una chiave è più sicuro.

  > [!NOTE]
  > I passaggi in questo articolo presuppongono che venga usata una password.


## <a name="automate-cluster-creation"></a>Automatizzare la creazione del cluster

È possibile automatizzare la creazione di istanze di HDInsight R Server usando modelli di Azure Resource Manager, l'SDK e PowerShell.

* Per creare un'istanza di R Server con un modello di Azure Resource Manager, vedere [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Distribuire un cluster HDInsight R Server).
* Per creare un'istanza di R Server con .NET SDK, vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Per distribuire R Server tramite PowerShell, vedere [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Creare un cluster usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Nuovo** > **Intelligence e analisi** > **HDInsight**.

    ![Immagine della creazione di un nuovo cluster](./media/r-server-get-started/newcluster.png)

3. Nell'esperienza **Creazione rapida** specificare il nome del cluster nella casella **Nome cluster**. Se sono disponibili più sottoscrizioni di Azure, usare la casella **Sottoscrizione** per selezionare quella da usare.

    ![Selezione del nome del cluster e della sottoscrizione](./media/r-server-get-started/clustername.png)

4. Selezionare **Tipo di cluster** per aprire il riquadro **Configurazione cluster**. Nel riquadro **Configurazione cluster** selezionare le opzioni seguenti:

    * **Tipo di cluster**: selezionare **R Server**.
    * **Versione**: selezionare la versione di R Server da installare nel cluster. La versione attualmente disponibile è **R Server 9.1 (HDI 3.6)**. Le note sulla versione per le versioni disponibili di R Server sono disponibili in [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).
    * **R Studio Community Edition for R Server**: questo IDE basato su browser viene installato per impostazione predefinita sul nodo perimetrale. Se si preferisce non installarlo, deselezionare la casella di controllo. Se si sceglie di installarlo, l'URL per l'accesso a RStudio Server è disponibile in un riquadro applicazione del portale per il cluster, dopo che è stato creato.
    * Lasciare le altre opzioni impostate sui valori predefiniti e usare il pulsante **Seleziona** per salvare il tipo di cluster.

        ![Screenshot del riquadro "Tipo di cluster"](./media/r-server-get-started/clustertypeconfig.png)

5. Nel riquadro **Informazioni di base** per le caselle **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster** immettere (rispettivamente) un nome utente e la password per il cluster.

6. Nella casella **Nome utente Secure Shell (SSH)** specificare il nome utente SSH. SSH viene usato per connettersi al cluster in modalità remota tramite un client SSH. È possibile specificare l'utente SSH in questa casella o dopo aver creato il cluster (nella scheda **Configurazione** per il cluster).
   
   > [!NOTE] 
   > R Server è configurato in modo da prevedere il nome utente SSH "remoteuser". Se si usa un nome utente diverso, è necessario eseguire un passaggio aggiuntivo dopo la creazione del cluster.

7. Lasciare selezionata la casella di controllo **Usa la stessa password dell'account di accesso del cluster** per usare la **PASSWORD** come tipo di autenticazione a meno che non si preferisca usare una chiave pubblica. Se si vuole accedere a R Server nel cluster tramite un client remoto, ad esempio R Tools per Visual Studio, RStudio o un altro ambiente IDE desktop, è necessario usare una coppia di chiavi pubblica/privata. Se si installa RStudio Server Community Edition, è necessario scegliere una password SSH.     

   Per creare e usare una coppia di chiavi pubblica/privata, deselezionare la casella **Usa la stessa password dell'account di accesso del cluster**. Selezionare quindi **CHIAVE PUBBLICA** e procedere come indicato di seguito. Queste istruzioni presuppongono che Cygwin con ssh-keygen o equivalente sia già installato.

   a. Dal prompt dei comandi sul computer portatile, generare una coppia di chiavi pubblica/privata:

        ssh-keygen -t rsa -b 2048

   b. Attenersi alla richiesta di un nome di un file di chiave e quindi immettere una passphrase per una maggiore sicurezza. La schermata dovrebbe essere simile all'immagine seguente:

      ![Riga di comando SSH in Windows](./media/r-server-get-started/sshcmdline.png)

      Questo comando crea un file di chiave privata e un file di chiave pubblica con il nome <nomefile-chiave-privata>.pub. In questo esempio i file sono furiosa e furiosa.pub:

      ![Risultato di esempio del comando dir](./media/r-server-get-started/dir.png)

   c. Specificare il file di chiave pubblica (&#42;.pub) durante l'assegnazione delle credenziali del cluster HDI. Confermare il gruppo di risorse e l'area, quindi selezionare **Avanti**.

      ![Riquadro Credenziali](./media/r-server-get-started/publickeyfile.png)  

   d. Modificare le autorizzazioni per il file di chiave privata nel computer portatile:

        chmod 600 <private-key-filename>

   e. Usare il file di chiave privata con SSH per l'accesso remoto:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      In alternativa, usare il file di chiave privata come parte della definizione del contesto di calcolo di Hadoop Spark per R Server nel client. Per altre informazioni, vedere come [creare un contesto di calcolo per Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark).

8. L'esperienza di creazione rapida indirizza l'utente al riquadro **Archiviazione**, dove sarà possibile selezionare le impostazioni dell'account di archiviazione da usare per la località primaria del file system HDFS usato dal cluster. Selezionare un account di archiviazione di Azure nuovo o esistente oppure un account Azure Data Lake Store esistente.

    - Per un account di archiviazione di Azure esistente, scegliere **Selezionare un account di archiviazione** e quindi l'account pertinente. Per creare un nuovo account, usare il collegamento **Crea nuovo** nella sezione **Selezionare un account di archiviazione**.

      > [!NOTE]
      > Se si seleziona **Nuovo** è necessario immettere un nome per il nuovo account di archiviazione. Se il nome viene accettato, verrà visualizzato un segno di spunta verde.

      Il **contenitore predefinito** viene impostato sul nome predefinito del cluster. Lasciare questa impostazione predefinita come valore.

      Se è stato selezionato un account di archiviazione nuovo, nel messaggio selezionare un'area in **Località**.  

         ![Riquadro Origine dati](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > La selezione del percorso per l'origine dati predefinito imposta anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

    - Per usare un account Data Lake Store esistente, selezionare l'account desiderato. Aggiungere quindi l'identità di *AAD* al cluster per consentire l'accesso all'archivio. Per altre informazioni su questo processo, vedere [Creare cluster HDInsight con Data Lake Store tramite il portale di Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Usare il pulsante **Seleziona** per salvare la configurazione dell'origine dati.


9. Verrà visualizzato il riquadro **Riepilogo** per convalidare le impostazioni. Qui è possibile modificare le dimensioni del cluster per cambiare il numero di server nel cluster e specificare le eventuali azioni script da eseguire. Se non è necessario un cluster di maggiori dimensioni, lasciare il numero di nodi del ruolo di lavoro sul valore predefinito di **4**. Il riquadro mostra anche il costo stimato del cluster.

    ![Riepilogo del cluster](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Se necessario, è possibile ridimensionare il cluster in un secondo momento tramite il portale (**Cluster** > **Impostazioni** > **Ridimensiona cluster**) per aumentare o ridurre il numero di nodi del ruolo di lavoro. Questo ridimensionamento può essere utile per disattivare il cluster quando non è in uso o per aggiungere capacità allo scopo di soddisfare le esigenze di attività di dimensioni più elevate.
   >
   >

   Ecco alcuni fattori da tenere presente per il dimensionamento del cluster, dei nodi di dati e del nodo perimetrale:  

   * Quando la quantità di dati è ingente, le prestazioni delle analisi R Server distribuite in Spark sono proporzionali al numero di nodi di lavoro.  

   * Le prestazioni delle analisi Server R sono proporzionali alle dimensioni dei dati analizzati. Ad esempio:   

     * Per quantità di dati di piccole e medie dimensioni, le prestazioni sono migliori se l'analisi dei dati viene eseguita in un contesto di calcolo locale sul nodo perimetrale. Per altre informazioni sugli scenari in cui i contesti di calcolo Spark e locale funzionano meglio, vedere [Opzioni del contesto di calcolo per R Server su HDInsight](r-server-compute-contexts.md).<br>
     * Se si accede al nodo perimetrale e si esegue lo script R, tutte le funzioni, ad eccezione delle funzioni ScaleR rx, vengono eseguite *localmente* sul nodo perimetrale, in modo tale che la memoria e il numero di core del nodo perimetrale vengano dimensionati di conseguenza. Lo stesso vale se si utilizza R Server su HDI come contesto di calcolo remoto dal computer portatile.

   Usare il pulsante **Seleziona** per salvare la configurazione dei piani tariffari del nodo.

   ![Riquadro per i piani tariffari del nodo](./media/r-server-get-started/pricingtier.png)

   È presente anche un collegamento **Scarica modello e parametri**. Fare clic su questo collegamento per visualizzare gli script che si possono usare per automatizzare la creazione di un cluster con la configurazione selezionata. Questi script sono anche disponibili dalla voce del portale di Azure per il cluster dopo che è stato creato.

   > [!NOTE]
   > La creazione del cluster richiede tempo, in genere circa 20 minuti. Per controllare il processo di creazione, usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Connettersi a RStudio Server

Se si è scelto di includere RStudio Server Community Edition nell'installazione è possibile accedere a RStudio in due modi:

- Passare all'URL seguente (dove *CLUSTERNAME* è il nome del cluster creato):

    https://*CLUSTERNAME*.azurehdinsight.net/rstudio/

- Aprire la voce relativa al cluster nel portale di Azure e selezionare il collegamento rapido **Dashboard di R Server** e quindi **Dashboard di R Studio**:

  ![Accedere al dashboard di RStudio](./media/r-server-get-started/rstudiodashboard1.png)

  ![Accedere al dashboard di RStudio](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> Indipendentemente dal metodo usato, al primo accesso è necessario eseguire l'autenticazione due volte. Alla prima autenticazione, specificare l'ID utente e la password amministratore del cluster. Alla seconda richiesta, specificare l'ID utente e la password SSH. Gli accessi successivi richiederanno solo l'ID utente e la password SSH.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Connettersi al nodo perimetrale di R Server

Connettersi al nodo perimetrale R Server del cluster HDInsight usando SSH con il comando seguente:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> L'indirizzo `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` è disponibile nel portale di Azure. Selezionare il cluster e quindi **Tutte le impostazioni** > **App** > **RServer**. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.
>
> ![Immagine dell'endpoint SSH per il nodo perimetrale](./media/r-server-get-started/sshendpoint.png)
>
>

Se è stata usata una password per proteggere l'account utente SSH, viene richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo aver stabilito la connessione, verrà visualizzato un prompt come quello che segue:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Abilitare più utenti simultanei

È possibile abilitare più utenti simultanei aggiungendo altri utenti per il nodo perimetrale in cui viene eseguita la versione Community di RStudio.

Quando si crea un cluster HDInsight, è necessario specificare due utenti: un utente HTTP e un utente SSH.

![Immissione delle credenziali per l'utente del cluster e l'utente SSH](./media/r-server-get-started/concurrent-users-1.png)

- **Nome utente dell'account di accesso del cluster**: utente HTTP per l'autenticazione tramite il gateway HDInsight usato per proteggere i cluster HDInsight creati. Questo utente HTTP viene usato per accedere all'interfaccia utente di Ambari, all'interfaccia utente di YARN e ad altri componenti di interfaccia utente.
- **Nome utente Secure Shell (SSH)**: utente SSH per accedere al cluster tramite Secure Shell. È un utente del sistema Linux per tutti i nodi head, di lavoro e perimetrali. È così possibile usare SSH per accedere a qualsiasi nodo in un cluster remoto.

La versione RStudio Server Community usata nel cluster di tipo Microsoft R Server in HDInsight accetta solo il nome utente e una password Linux come meccanismo di accesso. Non supporta il passaggio di token. Se è stato creato un nuovo cluster e si vuole usare RStudio per accedervi, è necessario eseguire l'accesso due volte.

1. Accedere con le credenziali utente HTTP tramite il gateway HDInsight: 

    ![Immissione delle credenziali utente HTTP](./media/r-server-get-started/concurrent-users-2a.png)

2. Usare le credenziali utente SSH per accedere a RStudio:
  
    ![Immissione delle credenziali utente SSH](./media/r-server-get-started/concurrent-users-2b.png)

Attualmente, durante il provisioning di un cluster HDInsight è possibile creare un solo account utente SSH. Per consentire a più utenti di accedere a cluster Microsoft R Server in HDInsight, è quindi necessario creare utenti aggiuntivi nel sistema Linux.

Poiché RStudio Server Community è in esecuzione nel nodo perimetrale del cluster, è necessario completare tre passaggi:

1. Usare l'utente SSH creato per accedere al nodo perimetrale.
2. Aggiungere altri utenti Linux nel nodo perimetrale.
3. Usare la versione Community di RStudio con l'utente creato.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Usare l'utente SSH creato per accedere al nodo perimetrale

Scaricare uno strumento SSH qualsiasi, ad esempio PuTTY, e usare l'utente SSH esistente per eseguire l'accesso. Seguire quindi le istruzioni riportate in [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) per accedere al nodo perimetrale. L'indirizzo del nodo perimetrale per R Server nel cluster HDInsight è: **clustername-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Aggiungere altri utenti Linux al nodo perimetrale

Per aggiungere un utente al nodo perimetrale, eseguire questi comandi:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Dovrebbero essere restituite le voci seguenti: 

![Output dei comandi sudo](./media/r-server-get-started/concurrent-users-3.png)

Quando viene richiesto di immettere la password Kerberos corrente, premere INVIO per ignorare la richiesta. L'opzione `-m` nel comando `useradd` indica che il sistema creerà una home directory per l'utente. Questa directory è obbligatoria per l'uso della versione Community di RStudio.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Usare la versione Community di RStudio con l'utente creato

Usare l'utente creato per accedere a RStudio:

![Pagina di accesso di RStudio](./media/r-server-get-started/concurrent-users-4.png)

Si noti che RStudio indica che si sta usando il nuovo utente, in questo caso **sshuser6**, per l'accesso al cluster: 

![Posizione del nuovo utente nella barra dei comandi di RStudio](./media/r-server-get-started/concurrent-users-5.png)

È anche possibile accedere simultaneamente da un'altra finestra del browser con le credenziali originali, per impostazione predefinita, **sshuser**.

È possibile inviare un processo usando le funzioni ScaleR. Di seguito è riportato un esempio dei comandi usati per eseguire un processo:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Si noti che i processi inviati sono associati a nomi utente diversi nell'interfaccia utente di YARN:

![Elenco di utenti nell'interfaccia utente di YARN](./media/r-server-get-started/concurrent-users-6.png)

Si noti anche che agli utenti appena aggiunti non sono assegnati privilegi radice nel sistema Linux. Tuttavia hanno diritto allo stesso accesso a tutti i file nel file system HDFS remoto e nell'archivio BLOB.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Usare la console di R

1. Nella sessione SSH usare il comando seguente per avviare la console R:  

        R

2. L'output dovrebbe essere simile al seguente:
    
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

3. Dal prompt `>` è possibile immettere codice R. R Server include pacchetti che consentono di interagire facilmente con Hadoop ed eseguire calcoli distribuiti. Ad esempio, usare il comando seguente per visualizzare la radice del file system predefinito per il cluster HDInsight:

        rxHadoopListFiles("/")

4. È anche possibile usare l'indirizzamento in stile archivio BLOB:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Usare R Server in HDI da un'istanza remota di Microsoft R Server o Microsoft R Client

È possibile configurare l'accesso al contesto di calcolo Hadoop Spark HDI da un'istanza remota di Microsoft R Server o Microsoft R Client in esecuzione in un computer desktop o portatile. Per altre informazioni, vedere la sezione "Using Microsoft R Server as a Hadoop Client" (Uso di Microsoft R Server come client Hadoop) in [Create a Compute Context for Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Creare un contesto di calcolo per Spark). A tale scopo, specificare le opzioni seguenti quando si definisce il contesto di calcolo RxSpark nel computer portatile: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Ad esempio:


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


## <a name="use-a-compute-context"></a>Usare un contesto di calcolo

Un contesto di calcolo consente di controllare se il calcolo viene eseguito localmente sul nodo perimetrale o distribuito sui nodi del cluster HDInsight.

1. Da RStudio Server o dalla console R (in una sessione SSH), usare il codice seguente per caricare dati di esempio nella risorsa di archiviazione predefinita per HDInsight:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Creare alcune informazioni sui dati e si definiscono due origini dati in modo da poter lavorare con i dati:

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Eseguire quindi una regressione logistica sui dati usando il contesto di calcolo locale:

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    L'output dovrebbe terminare con righe simili alle seguenti:

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

4. Eseguire la stessa regressione logistica usando il contesto di Spark. Il contesto di Spark distribuisce l'elaborazione su tutti i nodi del ruolo di lavoro del cluster HDInsight.

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
   > È anche possibile usare MapReduce per distribuire il calcolo sui nodi del cluster. Per altre informazioni sul contesto di calcolo, vedere [Opzioni del contesto di calcolo per R Server su HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuire il codice R su più nodi

Con R Server, è possibile prelevare facilmente il codice R esistente ed eseguirlo su più nodi del cluster tramite `rxExec`. Questa funzione è utile in caso di sweep di parametri o simulazioni. Il codice seguente è un esempio di come usare `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se si sta ancora usando il contesto Spark o MapReduce, questo comando restituisce il valore `nodename` per i nodi del ruolo di lavoro in cui viene eseguito il codice `(Sys.info()["nodename"])`. In un cluster a quattro nodi, ad esempio, verrà restituito un output simile al seguente:

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


## <a name="access-data-in-hive-and-parquet"></a>Accedere ai dati in Hive e Parquet

Una funzionalità disponibile in R Server 9.1 consente l'accesso diretto ai dati in Hive e Parquet per l'uso da parte delle funzioni di ScaleR nel contesto di calcolo di Spark. Queste funzionalità sono disponibili tramite le nuove funzioni dell'origine dati ScaleR denominate RxHiveData e RxParquetData. Queste funzioni vengono usate tramite Spark SQL per caricare i dati direttamente in un dataframe Spark e analizzarli tramite ScaleR.  

Il codice seguente mostra alcuni esempi di come usare le nuove funzioni:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Per altre informazioni su queste nuove funzioni, vedere la guida online in R Server usando i comandi `?RxHivedata` e `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Installare pacchetti R aggiuntivi nel nodo perimetrale

Per installare pacchetti R aggiuntivi nel nodo perimetrale, è possibile usare `install.packages()` direttamente dall'interno della console R quando si è connessi al nodo perimetrale tramite SSH. Tuttavia, se si desidera installare pacchetti R sui nodi del ruolo di lavoro del cluster, è necessario usare un'azione script.

Le azioni script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight o per installare software aggiuntivo, ad esempio altri pacchetti R. Per installare altri pacchetti tramite un'azione script, seguire questa procedura.

> [!IMPORTANT]
> È possibile usare le azioni script per installare altri pacchetti R solo dopo aver creato il cluster. Non usare questa procedura durante la creazione del cluster poiché per l'esecuzione dello script è necessario che R Server sia completamente installato e configurato.
>
>

1. Nel [Portale di Azure](https://portal.azure.com)selezionare il proprio R Server sul cluster HDInsight.

2. Nel riquadro **Impostazioni** selezionare **Azioni script** > **Invia nuova**.

   ![Immagine del riquadro Azioni script](./media/r-server-get-started/scriptaction.png)

3. Immettere le informazioni seguenti nel riquadro **Invia azione script**:

   * **Nome**: nome descrittivo per identificare lo script.

   * **URI script Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Head**: questa voce deve essere deselezionata.

   * **Ruolo di lavoro**: questa voce deve essere deselezionata.

   * **Zookeeper**: questa voce deve essere deselezionata.

   * **Nodi perimetrali**: questa voce deve essere selezionata.

   * **Parametri**: pacchetti R da installare, ad esempio `bitops stringr arules`.

   * **Salvare questa azione script in modo permanente...**: questa voce deve essere selezionata.  

   > [!NOTE]
   > Per impostazione predefinita, tutti i pacchetti R vengono installati da uno snapshot del repository Microsoft R Application Network coerente con la versione di R Server installata. L'installazione di versioni più recenti dei pacchetti espone al rischio di incompatibilità. Questa tipologia di installazione è tuttavia possibile specificando `useCRAN` come primo elemento dell'elenco dei pacchetti, ad esempio `useCRAN bitops, stringr, arules`.  
   > 
   > Alcuni pacchetti R richiedono librerie di sistema di Linux aggiuntive. Per praticità sono state preinstallate le dipendenze necessarie per i 100 pacchetti R più diffusi. Se i pacchetti R installati richiedono altre librerie, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico in Archiviazione di Azure e usare lo script modificato per installare i pacchetti.
   >
   > Per altre informazioni sullo sviluppo di azioni script, vedere l'articolo [Sviluppo di azioni script con HDInsight](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Aggiunta di un'azione script](./media/r-server-get-started/submitscriptaction.png)

4. Selezionare **Crea** per eseguire lo script. Al termine dell'esecuzione dello script, i pacchetti R sono disponibili su tutti i nodi del ruolo di lavoro.


## <a name="configure-microsoft-r-server-operationalization"></a>Configurare Microsoft R Server per la messa in funzione

Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni. Per configurare la messa in funzione di Microsoft R Server, seguire questa procedura:

1. Usare il comando `ssh` per il nodo perimetrale, ad esempio: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Cambiare la directory per la versione pertinente e usare il comando `sudo dotnet` per il file DLL. 

   Per Microsoft R Server 9.1:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Per Microsoft R Server 9.0:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Per configurare la messa in funzione di Microsoft R Server con una configurazione One-box, procedere come segue:

   a. Selezionare `Configure R Server for Operationalization`.

   b. Selezionare `A. One-box (web + compute nodes)`.

   c. Immettere una password per l'utente `admin`.

   ![Messa in funzione One-box](./media/r-server-get-started/admin-util-one-box-.png)

4. Come passaggio facoltativo, è possibile eseguire un test di diagnostica, come illustrato di seguito:

   a. Selezionare `6. Run diagnostic tests`.

   b. Selezionare `A. Test configuration`.

   c. Immettere `admin` per il nome utente e immettere la password usata nel passaggio di configurazione precedente.

   d. Confermare `Overall Health = pass`.

   e. Uscire dall'utilità di amministrazione.

   f. Uscire da SSH.

   ![Diagnostica per la messa in funzione](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Se si riscontrano ritardi prolungati quando si prova a utilizzare un servizio Web creato con le funzioni mrsdeploy in un contesto di calcolo di Spark, potrebbe essere necessario aggiungere alcune cartelle mancanti. L'applicazione Spark appartiene a un utente chiamato *rserve2* quando viene richiamata da un servizio Web usando le funzioni mrsdeploy. Per risolvere questo problema:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


A questo punto la configurazione per la messa in funzione è completata. È ora possibile usare il pacchetto mrsdeploy in R Client per connettersi alla messa in funzione nel nodo perimetrale. Si può quindi iniziare a usarne le funzionalità, come l'[esecuzione remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e i [servizi Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). A seconda che il cluster sia configurato o meno su una rete virtuale, potrebbe essere necessario impostare il tunneling di inoltro alla porta tramite l'accesso SSH.

### <a name="r-server-cluster-on-a-virtual-network"></a>Cluster R Server su una rete virtuale

Verificare che sia consentito il traffico attraverso la porta 12800 verso il nodo perimetrale. In questo modo è possibile usare tale nodo per la connessione alla funzionalità di messa in funzione.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` non può connettersi al nodo perimetrale ma è possibile accedere a tale nodo tramite SSH, è necessario verificare se la regola che consente il traffico sulla porta 12800 è stata impostata correttamente. Se il problema persiste, può essere risolto configurando il tunneling di port forwarding tramite SSH. Per istruzioni, vedere la sezione seguente.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Cluster R Server non configurato su una rete virtuale

Se il cluster non è configurato su una rete virtuale o si riscontrano problemi di connettività tramite la rete virtuale, è possibile usare il tunneling di port forwarding SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

È anche possibile configurarlo in PuTTY:

![Connessione SSH con PuTTY](./media/r-server-get-started/putty.png)

Quando la sessione SSH è attiva, il traffico proveniente dalla porta 12800 del computer viene inoltrato alla porta 12800 del nodo perimetrale tramite la sessione SSH. Assicurarsi di usare `127.0.0.1:12800` nel metodo `remoteLogin()`. Questo metodo accede alla messa in funzione del nodo perimetrale tramite port forwarding.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Ridimensionare i nodi di calcolo della messa in funzione di Microsoft R Server nei nodi del ruolo di lavoro di HDInsight

### <a name="decommission-the-worker-nodes"></a>Rimuovere le autorizzazioni dei nodi del ruolo di lavoro

Attualmente Microsoft R Server non è gestito tramite Yarn. Se è necessario rimuovere le autorizzazioni dei nodi dei ruoli di lavoro, lo strumento di gestione risorse di YARN non funzionerà come previsto in quanto non sarà in grado di riconoscere le risorse usate dal server. Per evitare questa situazione, è consigliabile rimuovere le autorizzazioni dei nodi del ruolo di lavoro prima di aumentare il numero dei nodi di calcolo.

Per rimuovere le autorizzazioni dei nodi del ruolo di lavoro:

1. Accedere alla console Ambari del cluster HDI e selezionare la scheda **Host**.
2. Selezionare i nodi del ruolo di lavoro di cui rimuovere le autorizzazioni e fare clic su **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **Host** > **Turn On Maintenance Mode (Attiva modalità di manutenzione)**. Ad esempio nell'immagine seguente i nodi selezionati per la rimozione delle autorizzazioni sono wn3 e wn4.  

   ![Screenshot dei comandi per attivare la modalità di manutenzione](./media/r-server-get-started/get-started-operationalization.png)  

3. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **DataNodes (Nodi dati)** > **Decommission (Rimuovi autorizzazioni)**.
4. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **NodeManagers (Gestori dati)** > **Decommission (Rimuovi autorizzazioni)**.
5. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **DataNodes (Nodi dati)** > **Stop (Arresta)**.
6. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **NodeManagers (Gestori dati)** > **Stop (Arresta)**.
7. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **Hosts (Host)** > **Stop All Components (Arresta tutti i componenti)**.
8. Deselezionare i nodi del ruolo di lavoro e selezionare i nodi head.
9. Selezionare **Actions (Azioni)** > **Selected Hosts (Host selezionati)** > **Hosts (Host)** > **Restart All Components (Riavvia tutti i componenti)**.

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Configurare i nodi di calcolo su ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione

1. Usare SSH per connettersi a ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione.
2. Eseguire un'utilità di amministrazione con `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Immettere `1` per selezionare l'opzione `Configure R Server for Operationalization`.
4. Immettere `c` per selezionare l'opzione `C. Compute node`. Questo passaggio configura il nodo di calcolo sul nodo del ruolo di lavoro.
5. Uscire dall'utilità di amministrazione.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Aggiungere i dettagli dei nodi di calcolo nel nodo Web

Dopo che tutti i nodi di lavoro per i quali è stata rimossa l'autorizzazione sono stati configurati per l'esecuzione nel nodo di calcolo, tornare al nodo perimetrale e aggiungere gli indirizzi IP di tali nodi di lavoro nella configurazione del nodo Web di Microsoft R Server:

1. Usare SSH per connettersi al nodo perimetrale.
2. Eseguire `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Individuare la sezione `URIs` e aggiungere i dettagli relativi alla porta e all'indirizzo IP del nodo del ruolo di lavoro.

    ![Riga di comando per il nodo perimetrale](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi durante la creazione dei cluster HDInsight, vedere i [requisiti di controllo di accesso](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Passaggi successivi

A questo punto, sono state acquisite le informazioni su come creare un cluster HDInsight che include R Server e si sono anche apprese le nozioni di base per l'uso della console R da una sessione SSH. Gli argomenti seguenti illustrano altre modalità di gestione e uso di R Server in HDInsight:

* [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](r-server-storage.md)
