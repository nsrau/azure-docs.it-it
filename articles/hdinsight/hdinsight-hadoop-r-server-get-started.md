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
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 89fa80b3e3409b7cd2f600776fffdeb3a5271b5d
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---
# <a name="get-started-using-r-server-on-hdinsight"></a>Introduzione all'uso di R Server su HDInsight

HDInsight include un'opzione R Server da integrare nel cluster HDInsight. Con questa opzione, gli script R possono usare Spark e MapReduce per eseguire i calcoli distribuiti. In questo documento si apprenderà come creare un R Server in un cluster HDInsight e come eseguire uno script R che illustra l'uso di Spark per i calcoli R distribuiti.


## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**: prima di iniziare questa esercitazione è necessario avere una sottoscrizione di Azure. Per altre informazioni, vedere il video su come [ottenere una versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un client Secure Shell (SSH)**: il client SSH viene usato per connettersi da remoto al cluster HDInsight ed eseguire i comandi direttamente nel cluster. Per altre informazioni, vedere l'articolo su come [usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
* **Chiavi SSH (facoltative)**: è possibile proteggere l'account SSH usato per connettersi al cluster mediante una password o una chiave pubblica. Usare una password è più semplice ed è possibile iniziare senza necessità di creare una coppia di chiavi pubblica/privata. Tuttavia, usare una chiave è più sicuro.

> [!NOTE]
> I passaggi in questo documento presuppongono che si stia usando una password.


## <a name="automated-cluster-creation"></a>Creazione automatizzata di cluster

È possibile automatizzare la creazione di cluster HDInsight R Server usando modelli di Azure Resource Manager, SDK e PowerShell.

* Per creare un cluster R Server con un modello di Azure Resource Manager, vedere [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Distribuire un cluster HDInsight R Server).
* Per creare un cluster R Server con .NET SDK, vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Per distribuire R Server con PowerShell, vedere l'articolo sulla [creazione di un cluster R Server in HDInsight con PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Creare il cluster con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Nuovo** -> **Intelligence e analisi** -> **HDInsight**.

    ![Immagine della creazione di un nuovo cluster](./media/hdinsight-hadoop-r-server-get-started/newcluster.png)

3. Nell'esperienza **Creazione rapida** inserire il nome del cluster nel campo **Nome cluster**. Se sono disponibili più sottoscrizioni di Azure, usare la voce **Sottoscrizione** per selezionare quella da usare.

    ![Selezione del nome del cluster e della sottoscrizione](./media/hdinsight-hadoop-r-server-get-started/clustername.png)

4. Selezionare **Tipo di cluster** per aprire il pannello **Configurazione cluster**. Nel pannello **Configurazione cluster** selezionare le opzioni seguenti:

    * **Tipo di cluster**: R Server
    * **Versione**: selezionare la versione di R Server da installare nel cluster. La versione attualmente disponibile è ***R Server 9.1 (HDI 3.6)***. Le note sulla versione per le versioni disponibili di R Server sono disponibili [qui](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **R Studio community edition for R Server**: questo IDE basato su browser viene installato per impostazione predefinita sul nodo perimetrale. Se si preferisce non installarlo, deselezionare la casella di controllo. Se si sceglie di installarlo, l'URL per l'accesso a RStudio Server è disponibile in un pannello delle applicazioni del portale per il cluster, dopo la creazione.
    * Lasciare le altre opzioni impostate sui valori predefiniti e usare il pulsante **Seleziona** per salvare il tipo di cluster.

        ![Schermata del pannello del tipo di cluster](./media/hdinsight-hadoop-r-server-get-started/clustertypeconfig.png)

5. Immettere il **nome utente dell'account di accesso del cluster** e la **password dell'account di accesso del cluster**.

    Specificare un **nome utente SSH**. SSH è usato per connettersi al cluster da remoto tramite un client **Secure Shell (SSH)**. È possibile specificare l'utente SSH in questa finestra di dialogo o dopo aver creato il cluster (nella scheda Configurazione per il cluster). R Server è configurato in modo da prevedere il **nome utente SSH** "remoteuser".  **Se si usa un nome utente diverso, è necessario eseguire un passaggio aggiuntivo dopo la creazione del cluster.**

    Lasciare selezionata la casella **Usa la stessa password dell'account di accesso del cluster** per usare la **PASSWORD** come tipo di autenticazione a meno che non si preferisca usare una chiave pubblica.  Se si vuole accedere a R Server nel cluster tramite un client remoto, ad esempio RTVS, RStudio o un altro ambiente desktop IDE, è necessario usare una coppia di chiavi pubblica/privata. Se si installa RStudio Server Community Edition, è necessario scegliere una password SSH.     

    Per creare e usare una coppia di chiavi pubblica/privata, deselezionare **Usa la stessa password dell'account di accesso del cluster** e quindi selezionare **CHIAVE PUBBLICA** e procedere come segue. Queste istruzioni presuppongono che Cygwin con ssh-keygen o equivalente sia già installato.

    * Dal prompt dei comandi sul computer portatile, generare una coppia di chiavi pubblica/privata:

        ssh-keygen -t rsa -b 2048

    * Attenersi alla richiesta di un nome di un file di chiave e quindi immettere una passphrase per una maggiore sicurezza. La schermata dovrebbe essere simile all'immagine seguente:

        ![Riga di comando SSH in Windows](./media/hdinsight-hadoop-r-server-get-started/sshcmdline.png)

    * Questo comando crea un file di chiave privata e un file di chiave pubblica con il nome <nomefile-chiave-privata>.pub, ad esempio furiosa e furiosa.pub.

        ![Dir SSH](./media/hdinsight-hadoop-r-server-get-started/dir.png)

    * Specificare quindi il file di chiave pubblica (&#42;.pub) quando si assegnano le credenziali del cluster HDI e infine confermare il gruppo di risorse e l'area e selezionare **Avanti**.

        ![Pannello Credenziali](./media/hdinsight-hadoop-r-server-get-started/publickeyfile.png)  

   * Modificare le autorizzazioni per il file di chiave privata nel computer portatile:

        chmod 600 <private-key-filename>

   * Usare il file di chiave privata con SSH per l'accesso remoto:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      o come parte della definizione del contesto di calcolo di Hadoop Spark per R Server nel client. Vedere la sottosezione **Using Microsoft R Server as a Hadoop Client** (Uso di Microsoft R Server come client Hadoop) in [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creare un contesto di calcolo per Spark).

6. La creazione rapida esegue la transizione al pannello **Archiviazione** per selezionare le impostazioni dell'account di archiviazione da usare per il percorso principale del file system HDFS usato dal cluster. Selezionare un account di archiviazione di Azure nuovo o esistente oppure un account di archiviazione di Data Lake esistente.

    - Se si sceglie un account di archiviazione di Azure, è possibile selezionare un account di archiviazione esistente scegliendo **Selezionare un account di archiviazione** e quindi selezionando l'account desiderato. Creare un nuovo account usando il collegamento **Nuovo** nella sezione **Selezionare un account di archiviazione**.

      > [!NOTE]
      > Se si seleziona **Nuovo** è necessario immettere un nome per il nuovo account di archiviazione. Se il nome viene accettato, verrà visualizzato un segno di spunta verde.

      Il **contenitore predefinito** viene impostato sul nome predefinito del cluster. Lasciare questa impostazione predefinita come valore.

      Se è stata selezionata l'opzione per un nuovo account di archiviazione, viene visualizzato un prompt che consente di selezionare il **Percorso** per scegliere l'area in cui creare l'account di archiviazione.  

         ![Pannello di origine dati](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > La selezione del percorso per l'origine dati predefinita imposta anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

    - Se si vuole usare un Data Lake Store esistente, selezionare l'account di archiviazione ADLS da usare e aggiungere al cluster l'identità *ADD* del cluster per consentire l'accesso all'archivio. Per altre informazioni su questo processo, vedere [Creare cluster HDInsight con Data Lake Store tramite il portale di Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Usare il pulsante **Seleziona** per salvare la configurazione dell'origine dati.


7. Viene quindi visualizzato il pannello **Riepilogo** per convalidare tutte le impostazioni. Qui è possibile modificare le **dimensioni dei Cluster** per cambiare il numero di server del cluster e specificare qualsiasi **azione di script** da eseguire. A meno che non si sia consapevoli di aver bisogno di un cluster di maggiori dimensioni, lasciare il numero di nodi di lavoro sul valore predefinito di `4`. All'interno del pannello viene visualizzato il costo stimato del cluster.

    ![riepilogo cluster](./media/hdinsight-hadoop-r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Se necessario, è possibile ridimensionare il cluster in un secondo momento tramite il portale (**Cluster** -> **Impostazioni** -> **Ridimensiona cluster**) per aumentare o ridurre il numero di nodi di lavoro.  Questo ridimensionamento può essere utile per disattivare il cluster quando non è in uso o per aggiungere capacità allo scopo di soddisfare le esigenze di attività più estese.
   >
   >

   Ecco alcuni fattori da tenere presente quando si modificano le dimensioni del cluster, dei nodi di dati e del nodo perimetrale:  

   * Quando la quantità di dati è ingente, le prestazioni delle analisi R Server distribuite in Spark sono proporzionali al numero di nodi di lavoro.  

   * Le prestazioni delle analisi Server R sono proporzionali alle dimensioni dei dati analizzati. ad esempio:  

     * Per quantità di dati di piccole e medie dimensioni, le prestazioni sono migliori se l'analisi avviene in un contesto di calcolo locale sul nodo perimetrale.  Per altre informazioni sugli scenari in cui i contesti di calcolo Spark e locale funzionano meglio, vedere Opzioni del contesto di calcolo per R Server su HDInsight.<br>
     * Se si accede al nodo perimetrale e si esegue lo script R, tutte le funzioni, ad eccezione delle funzioni ScaleR rx, vengono eseguite <strong>localmente</strong> sul nodo perimetrale, in modo tale che la memoria e il numero di core del nodo perimetrale vengano ridimensionati secondo le esigenze. Lo stesso vale se si utilizza R Server su HDI come contesto di calcolo remoto dal computer portatile.

     ![Pannello livelli dei prezzi di nodo](./media/hdinsight-hadoop-r-server-get-started/pricingtier.png)

     Usare il pulsante **Seleziona** per salvare la configurazione dei piani tariffari del nodo.

   È presente anche un collegamento **Scarica modello e parametri**. Fare clic su questo collegamento per visualizzare gli script utilizzabili per automatizzare la creazione di un cluster con la configurazione selezionata. Questi script sono anche disponibili dalla voce del portale di Azure per il cluster dopo che è stato creato.

   > [!NOTE]
   > La creazione del cluster richiede tempo, in genere circa 20 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Connettersi a RStudio Server

Se si è scelto di includere RStudio Server Community Edition nell'installazione è possibile accedere a RStudio in due modi diversi.

1. Passare all'URL seguente (dove **CLUSTERNAME** è il nome del cluster creato):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Aprire la voce relativa al cluster nel portale di Azure e selezionare il collegamento rapido **Dashboard di R Server** e quindi **Dashboard di R Studio**:

     ![Accedere al dashboard di R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Accedere al dashboard di R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Indipendentemente dal metodo usato, al primo accesso è necessario eseguire l'autenticazione due volte.  Alla prima autenticazione, specificare l'*ID utente* e la *password amministratore del cluster*. Alla seconda richiesta, specificare l'*ID utente* e la *password SSH*. Per gli accessi successivi sono necessari solo l'*ID utente* e la *password SSH*.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Connettersi al nodo perimetrale di R Server

Connettersi al nodo perimetrale R Server del cluster HDInsight usando SSH con il comando:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> È possibile trovare l'indirizzo `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` nel portale di Azure selezionando il cluster e quindi **Tutte le impostazioni** -> **App** -> **RServer**. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.
>
> ![Immagine dell'endpoint SSH per il nodo perimetrale](./media/hdinsight-hadoop-r-server-get-started/sshendpoint.png)
>
>

Se è stata usata una password per proteggere l'account utente SSH, viene richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. ad esempio:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

Una volta effettuata la connessione, viene visualizzato un prompt come quello che segue:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Abilitare più utenti simultanei

È possibile abilitare più utenti simultanei aggiungendo altri utenti per il nodo perimetrale in cui viene eseguita la versione Community di RStudio.

Quando si crea un cluster HDInsight, è necessario specificare due utenti: un utente HTTP e un utente SSH.

![Utenti simultanei 1](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-1.png)

- **Nome utente dell'account di accesso del cluster**: utente HTTP per l'autenticazione tramite il gateway HDInsight usato per proteggere i cluster HDInsight creati. Questo utente HTTP viene usato per accedere all'interfaccia utente di Ambari, all'interfaccia utente di YARN e ad altri componenti di interfaccia utente.
- **Nome utente Secure Shell (SSH)**: utente SSH per accedere al cluster tramite Secure Shell. È un utente del sistema Linux per tutti i nodi head, di lavoro e perimetrali. È così possibile usare Secure Shell per accedere a qualsiasi nodo di un cluster remoto.

La versione RStudio Server Community usata nel cluster di tipo Microsoft R Server in HDInsight accetta solo un nome utente e una password Linux come meccanismo di accesso. Non supporta il passaggio di token. Se si vuole usare RStudio per accedere a un nuovo cluster che si è creato, è necessario eseguire l'accesso due volte.

- Accedere prima con le credenziali utente HTTP tramite il gateway HDInsight: 

    ![Utenti simultanei 2a](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2a.png)

- Usare quindi le credenziali utente SSH per accedere a RStudio:
  
    ![Utenti simultanei 2b](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2b.png)

Attualmente, durante il provisioning di un cluster HDInsight è possibile creare un solo account utente SSH. Per consentire a più utenti di accedere a cluster Microsoft R Server in HDInsight, è quindi necessario creare utenti aggiuntivi nel sistema Linux.

Dato che RStudio Server Community è in esecuzione nel nodo perimetrale del cluster, si devono completare diversi passaggi:

1. Usare l'utente SSH creato per accedere al nodo perimetrale
2. Aggiungere altri utenti Linux nel nodo perimetrale
3. Usare la versione Community di RStudio con l'utente creato

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Passaggio 1: Usare l'utente SSH creato per accedere al nodo perimetrale

Scaricare qualsiasi strumento SSH (ad esempio, Putty) e usare l'utente SSH esistente per eseguire l'accesso. Seguire quindi le istruzioni riportate in [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md) per accedere al nodo perimetrale. L'indirizzo del nodo perimetrale per R Server nel cluster HDInsight è: *clustername-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>Passaggio 2: Aggiungere altri utenti Linux nel nodo perimetrale

Per aggiungere un utente al nodo perimetrale, eseguire questi comandi:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Dovrebbero essere restituite le voci seguenti: 

![Utenti simultanei 3](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-3.png)

Quando viene richiesto di immettere la password Kerberos corrente, è sufficiente premere **INVIO** per ignorare la richiesta. L'opzione `-m` nel comando `useradd` indica che il sistema creerà una home directory per l'utente, obbligatoria per la versione Community di RStudio.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Passaggio 3: Usare la versione Community di RStudio con l'utente creato

Usare l'utente creato per accedere a RStudio:

![Utenti simultanei 4](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-4.png)

Si noti che RStudio indica che si sta usando il nuovo utente (in questo caso, ad esempio, *sshuser6*) per l'accesso al cluster: 

![Utenti simultanei 5](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-5.png)

Si può anche accedere simultaneamente da un'altra finestra del browser con le credenziali originali (per impostazione predefinita, *sshuser*).

È possibile inviare un processo usando funzioni ScaleR. Di seguito è riportato un esempio dei comandi usati per eseguire un processo:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)


Si noti che i processi inviati sono associati a nomi utente diversi nell'interfaccia utente di YARN:

![Utenti simultanei 6](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-6.png)

Si noti anche gli utenti appena aggiunti non hanno privilegi a livello radice nel sistema Linux, ma hanno lo stesso accesso a tutti i file nella risorsa di archiviazione HDFS e WASB remota.


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

    Type "readme()" for release notes.
    >

3. Dal prompt `>` è possibile immettere codice R. R Server include pacchetti che consentono di interagire facilmente con Hadoop ed eseguire calcoli distribuiti. Ad esempio, usare il comando seguente per visualizzare la radice del file system predefinito per il cluster HDInsight:

        rxHadoopListFiles("/")

4. È anche possibile usare l'indirizzamento in stile WASB.

        rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Utilizzare R Server in HDI da un'istanza remota di Microsoft R Server o Microsoft R Client

È possibile configurare l'accesso al contesto di calcolo Hadoop Spark HDI da un'istanza remota di Microsoft R Server o Microsoft R Client in esecuzione in un computer desktop o portatile. Vedere la sottosezione **Using Microsoft R Server as a Hadoop Client** (Uso di Microsoft R Server come client Hadoop) in [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Creare un contesto di calcolo per Spark). A tale scopo, è necessario specificare le opzioni seguenti quando si definisce il contesto di calcolo RxSpark nel computer portatile: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. ad esempio:


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

4. Quindi, si prova a eseguire la stessa regressione logistica usando il contesto di Spark. Il contesto di Spark distribuisce l'elaborazione su tutti i nodi del ruolo di lavoro del cluster HDInsight.

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

Con R Server, è possibile prelevare facilmente il codice R esistente ed eseguirlo su più nodi del cluster tramite `rxExec`. Questa funzione è utile in caso di sweep di parametri o simulazioni. Il codice seguente è un esempio di come usare `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se si sta ancora usando il contesto Spark o MapReduce, questo comando restituisce il valore nodename per i nodi di lavoro in cui viene eseguito il codice `(Sys.info()["nodename"])`. In un cluster a quattro nodi, ad esempio, si può prevedere di ricevere un output simile al seguente:

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


## <a name="accessing-data-in-hive-and-parquet"></a>Accesso ai dati in Hive e Parquet

Una funzionalità disponibile in R Server 9.1 consente l'accesso diretto ai dati in Hive e Parquet per l'uso da parte delle funzioni di ScaleR nel contesto di calcolo di Spark. Queste funzionalità sono disponibili tramite nuove funzioni di origine dati ScaleR denominate RxHiveData e RxParquetData, che usano Spark SQL per caricare i dati direttamente in un frame di dati di Spark per l'analisi da parte di ScaleR.  

Il codice seguente offre un esempio dell'uso delle nuove funzioni:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
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

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Per altre informazioni sull'uso di queste nuove funzioni, vedere la guida in linea di R Server tramite i comandi `?RxHivedata` e `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Installare pacchetti R aggiuntivi nel nodo perimetrale

Per installare pacchetti R aggiuntivi nel nodo perimetrale, è possibile usare `install.packages()` direttamente dall'interno della console R quando si è connessi al nodo perimetrale tramite SSH. Tuttavia, se si desidera installare pacchetti R sui nodi di lavoro del cluster, è necessario usare un'azione di script.

Le azioni di script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight o per installare software aggiuntivo, ad esempio altri pacchetti R. Per installare altri pacchetti tramite un'azione di script, seguire questa procedura:

> [!IMPORTANT]
> Le azioni di script per installare altri pacchetti R sono possono essere usate solo dopo aver creato il cluster. Non usare questa procedura durante la creazione del cluster poiché lo script si basa su R Server completamente installato e configurato.
>
>

1. Nel [Portale di Azure](https://portal.azure.com)selezionare il proprio R Server sul cluster HDInsight.

2. Nel pannello **Impostazioni** selezionare **Azioni script** e quindi **Invia nuova** per inviare una nuova azione di script.

   ![Immagine del pannello Azioni script](./media/hdinsight-hadoop-r-server-get-started/scriptaction.png)

3. Immettere le informazioni seguenti nel pannello **Invia azione script**:

   * **Nome**: nome descrittivo per identificare lo script

   * **URI script Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Head**: questa voce deve essere **deselezionata**

   * **Lavoro**: questa voce deve essere **selezionata**

   * **Nodi perimetrali**: questa voce deve essere **deselezionata**

   * **Zookeeper**: questa voce deve essere **deselezionata**

   * **Parametri**: i pacchetti R da installare. Ad esempio, `bitops stringr arules`

   * **Salvare questa azione script in modo permanente...**: questa voce deve essere **selezionata**  

   > [!NOTE]
   > 1. Per impostazione predefinita, tutti i pacchetti R vengono installati da uno snapshot dell'archivio MRAN di Microsoft coerente con la versione di R Server che è stato installato. L'installazione di versioni più recenti dei pacchetti espone al rischio di incompatibilità. Questo tipo di installazione è tuttavia possibile specificando `useCRAN` come primo elemento dell'elenco dei pacchetti, ad esempio `useCRAN bitops, stringr, arules`.  
   > 2. Alcuni pacchetti R richiedono librerie di sistema di Linux aggiuntive. Per praticità sono state preinstallate le dipendenze necessarie per i 100 pacchetti R più diffusi. Tuttavia, se i pacchetti R installati richiedono altre librerie, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico su Archiviazione di Azure e usare lo script modificato per installare i pacchetti.
   >    Per altre informazioni sullo sviluppo di azioni script, vedere l'articolo [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Aggiunta di un'azione script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Selezionare **Crea** per eseguire lo script. Una volta completato lo script, i pacchetti R sono disponibili su tutti i nodi del ruolo di lavoro.


## <a name="using-microsoft-r-server-operationalization"></a>Uso della messa in funzione di Microsoft R Server

Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni. Per configurare la messa in funzione di Microsoft R Server, seguire questa procedura:

Innanzitutto, accedere tramite SSH al nodo perimetrale. Ad esempio, 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

dopo aver usato SSH passare alla directory della versione corrispondente ed eseguire sudo sulla DLL di dotnet: 

- Per Microsoft R Server 9.1:

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0 sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Per Microsoft R Server 9.0:

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Per configurare la messa in funzione di Microsoft R Server con una configurazione di una casella, procedere come segue:

1. Selezionare "Configure R Server for Operationalization" (Configurare R Server per la messa in funzione)
2. Selezionare "A. One-box (web + compute nodes)” (Una casella (Web + nodi di calcolo)"
3. Immettere una password per l'utente **admin**

![Opzione per una casella](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Come passaggio facoltativo, è possibile eseguire controlli diagnostici eseguendo un test di diagnostica, come illustrato di seguito:

1. Selezionare "6. Run diagnostic tests" (Esegui test diagnostici)
2. Selezionare "A. Test configuration" (Configurazione test)
3. Immettere nome utente = "admin" e la password del passaggio di configurazione precedente
4. Conferma dell'integrità complessiva = pass
5. Uscire dall'utilità di amministrazione
6. Uscire da SSH

![Diagnostica per la messa in funzione](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>**Ritardi considerevoli quando si utilizza il servizio Web in Spark**
>
>Se si riscontrano ritardi considerevoli quando si prova a utilizzare un servizio Web creato con le funzioni mrsdeploy in un contesto di calcolo di Spark, potrebbe essere necessario aggiungere alcune cartelle mancanti. L'applicazione Spark appartiene a un utente chiamato "*rserve2*" quando viene richiamata da un servizio Web usando le funzioni mrsdeploy. Per risolvere questo problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


A questo punto la configurazione per la messa in funzione è completata. È ora possibile usare il pacchetto "mrsdeploy" in RClient per connettersi alla messa in funzione sul nodo perimetrale e iniziare a usarne le funzionalità, ad esempio l'[esecuzione remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) e i [servizi Web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). A seconda che il cluster sia configurato o meno su una rete virtuale, potrebbe essere necessario impostare il tunneling di inoltro alla porta tramite l'accesso SSH. Le sezioni seguenti illustrano come configurare questo tunnel.

### <a name="rserver-cluster-on-virtual-network"></a>Cluster RServer su rete virtuale

Verificare che sia consentito il traffico attraverso la porta 12800 verso il nodo perimetrale. In questo modo è possibile usare tale nodo per la connessione alla funzionalità di messa in funzione.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` non può connettersi al nodo perimetrale ma è possibile accedere a tale nodo tramite SSH, è necessario verificare se la regola che consente il traffico sulla porta 12800 è stata impostata correttamente. Se il problema persiste, può essere risolto configurando il tunneling di port forwarding tramite SSH. Per istruzioni, vedere la sezione seguente.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster RServer non impostato su rete virtuale

Se il cluster non è configurato sulla rete virtuale o si riscontrano problemi relativi alla connettività tramite la rete virtuale, è possibile usare il tunneling di port forwarding SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

La configurazione è possibile anche in Putty.

![Connessione SSH con Putty](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Quando la sessione SSH è attiva, il traffico proveniente dalla porta 12800 del computer viene inoltrato alla porta 12800 del nodo perimetrale tramite la sessione SSH. Assicurarsi di usare `127.0.0.1:12800` nel metodo `remoteLogin()`. Viene così eseguito l'accesso alla messa in funzione del nodo perimetrale tramite port forwarding.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Procedura per ridimensionare i nodi di calcolo della messa in funzione di Microsoft R Server nei nodi del ruolo di lavoro di HDInsight

### <a name="decommission-the-worker-nodes"></a>Rimuovere le autorizzazioni dei nodi del ruolo di lavoro

Attualmente Microsoft R Server non è gestito tramite Yarn. Se è necessario rimuovere le autorizzazioni dei nodi del ruolo di lavoro, Yarn Resource Manager non funzionerà come previsto in quanto non sarà in grado di riconoscere le risorse impiegate dal server. Per evitare questa situazione, è consigliabile rimuovere le autorizzazioni dei nodi del ruolo di lavoro prima di ridimensionare i nodi di calcolo.

Passaggi per la rimozione delle autorizzazioni dei nodi del ruolo di lavoro:

* Accedere alla console Ambari del cluster HDI e fare clic sulla scheda "Host"
* Selezionare i nodi del ruolo di lavoro di cui rimuovere le autorizzazioni e fare clic su "Actions" (Azioni) > "Selected Hosts" (Host selezionati) > "Host" > e infine su "Turn ON Maintenance Mode" (Attiva modalità di manutenzione). Ad esempio nell'immagine seguente i nodi selezionati per la rimozione delle autorizzazioni sono wn3 e wn4.  

   ![Rimozione delle autorizzazioni dei nodi del ruolo di lavoro](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Selezionare **Actions** > **Selected Hosts** > **DataNodes** > fare clic su **Decommission** (Rimuovi autorizzazioni).
* Selezionare **Actions** > **Selected Hosts** > **NodeManagers** > fare clic su **Decommission** (Rimuovi autorizzazioni).
* Selezionare **Actions** > **Selected Hosts** > **DataNodes** > fare clic su **Stop** (Arresta).
* Selezionare **Actions** > **Selected Hosts** > **NodeManagers** > fare clic su **Stop** (Arresta).
* Selezionare **Actions** > **Selected Hosts** > **Hosts** > fare clic su **Stop All Components** (Arresta tutti i componenti).
* Deselezionare i nodi del ruolo di lavoro e selezionare i nodi head.
* Selezionare **Actions** > **Selected Hosts** > "**Hosts** > **Restart All Components** (Riavvia tutti i componenti).

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurare i nodi di calcolo su ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione

1. Accedere tramite SSH a ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione.
2. Eseguire l'utilità di amministrazione con `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Immettere "1" per selezionare l'opzione "Configure R Server for Operationalization" (Configurare R Server per la messa in funzione).
4. Immettere "c" per selezionare l'opzione "C. Compute node" (Nodo di calcolo). Il nodo di calcolo viene configurato sul nodo del ruolo di lavoro.
5. Uscire dall'utilità di amministrazione.

### <a name="add-compute-nodes-details-on-web-node"></a>Aggiungere i dettagli dei nodi di calcolo nel nodo Web

Dopo che tutti i nodi di lavoro per i quali è stata rimossa l'autorizzazione sono stati configurati per l'esecuzione del nodo di calcolo, tornare al nodo perimetrale e aggiungere gli indirizzi IP di tali nodi di lavoro nella configurazione del nodo Web Microsoft R Server:

* Accedere tramite SSH al nodo perimetrale.
* Eseguire `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Individuare la sezione dell'URI e aggiungere i dettagli relativi alla porta e all'IP del nodo del ruolo di lavoro.

    ![Rimozione delle autorizzazioni dei nodi del ruolo di lavoro con la riga di comando](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Passaggi successivi

A questo punto dovrebbe essere chiaro come creare un nuovo cluster HDInsight che includa R Server, comprese le nozioni di base sull'uso della console di R da una sessione SSH. Gli argomenti seguenti illustrano altre modalità di gestione e uso di R Server in HDInsight:

* [Aggiungere RStudio Server a HDInsight (se non installato durante la creazione del cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](hdinsight-hadoop-r-server-storage.md)

