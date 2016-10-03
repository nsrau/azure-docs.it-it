<properties
   pageTitle="Introduzione a R Server su HDInsight (anteprima) | Azure"
   description="Informazioni sulla creazione di un Apache Spark nel cluster HDInsight (Hadoop) che include R Server (anteprima) e sull'invio di uno script R nel cluster."
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
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# Introduzione all'uso di R Server su HDInsight (anteprima)

L'offerta del piano Premium per HDInsight include R Server nell'ambito del cluster HDInsight (anteprima). In questo modo gli script R possono usare MapReduce e Spark per eseguire i calcoli distribuiti. In questo documento si apprenderà come creare un nuovo R Server in HDInsight e come eseguire uno script R che illustra l'uso di Spark per i calcoli R distribuiti.

![Diagramma del flusso di lavoro del documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Prerequisiti

* __Una sottoscrizione di Azure__: prima di iniziare questa esercitazione, è necessario avere una sottoscrizione di Azure. Per altre informazioni, vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* __Un client Secure Shell (SSH)__: il client SSH viene usato per connettersi da remoto al cluster HDInsight e per eseguire i comandi direttamente sul cluster. I sistemi Linux, Unix e OS X offrono un client SSH tramite il comando `ssh`. Per i sistemi Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Chiavi SSH (facoltative)__: è possibile proteggere l'account SSH usato per connettersi al cluster mediante una password o una chiave pubblica. Usare una password è più semplice ed è possibile iniziare senza necessità di creare una coppia di chiavi pubblica/privata. Tuttavia, usare una chiave è più sicuro.
    
        I passaggi in questo documento presuppongono che si stia usando una password. Per informazioni su come creare e usare chiavi SSH con HDInsight, vedere i seguenti documenti:
        
        * [Usare SSH con HDInsight da client Linux, Unix oppure OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Uso di SSH con HDInsight da client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creare il cluster

> [AZURE.NOTE] La procedura descritta in questo documento crea un R Server in HDInsight mediante informazioni di configurazione di base. Per altre impostazioni di configurazione del cluster (ad esempio l'uso di account di archiviazione aggiuntivi, di una Rete virtuale di Azure o la creazione di un metastore per Hive), vedere [Creare cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare __NUOVO__, __Dati e analisi__, quindi selezionare __HDInsight__.

    ![Immagine della creazione di un nuovo cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Inserire il nome del cluster nel campo __Nome del cluster__. Se sono disponibili più sottoscrizioni di Azure, usare la voce __Sottoscrizione__ per selezionare quella da usare.

    ![Selezione del nome del cluster e della sottoscrizione](./media/hdinsight-getting-started-with-r/clustername.png)

4. Scegliere __Selezionare il tipo di cluster__. Nel pannello __Tipo di cluster__, selezionare le seguenti opzioni:

    * __Tipo di cluster__: R Server su Spark
    
    * __Livello cluster__: Premium

    Lasciare le altre opzioni impostate sui valori predefiniti, quindi usare il pulsante __Seleziona__ per salvare il tipo di cluster.
    
    ![Schermata del pannello del tipo di cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] È anche possibile aggiungere R Server ad altri tipi di cluster HDInsight (ad esempio Hadoop o HBase) selezionando il tipo di cluster e quindi scegliendo __Premium__.

5. Selezionare **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e quindi scegliere quello in cui creare il cluster. In alternativa, è possibile selezionare **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare che il nome del nuovo gruppo è disponibile.

    > [AZURE.NOTE] Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.
    
    Usare il pulsante __Seleziona__ per salvare il gruppo di risorse.

6. Selezionare **Credenziali**, quindi immettere un **Nome utente di accesso al Cluster** e la **Password dell'account di accesso Cluster**.

    Immettere un __nome utente SSH__. SSH è usato per connettersi al cluster da remoto tramite un client __Secure Shell (SSH)__. È possibile specificare l'utente SSH in questa finestra di dialogo o dopo aver creato il cluster (scheda Configurazione per il cluster). R Server è configurato in modo da prevedere un __nome utente SSH__ di "remoteuser". Se si utilizza un nome utente diverso, è necessario eseguire un passaggio aggiuntivo dopo la creazione del cluster.
    
    ![Pannello Credenziali](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __Tipo di autenticazione SSH__: selezionare __PASSWORD__ come tipo di autenticazione a meno che non si preferisca utilizzare una chiave pubblica. Se si desidera accedere a R Server nel cluster tramite un client remoto, ad esempio RTVS, RStudio o un altro ambiente desktop IDE, è necessario utilizzare una coppia di chiavi pubblica/privata.

	Per creare e utilizzare una coppia di chiavi pubblica/privata, selezionare 'CHIAVE PUBBLICA' e continuare come segue. Queste istruzioni presuppongono che Cygwin con ssh-keygen o equivalente sia già installato.

	-    Dal prompt dei comandi sul computer portatile, generare una coppia di chiavi pubblica/privata:
	  
		    ssh-keygen -t rsa -b 2048 –f <private-key-filename>
      
    -    Viene creato un file di chiave privata e un file di chiave pubblica con il nome <private-key-filename>.pub, ad esempio davec e davec.pub. Successivamente, specificare il file di chiave pubblica (*. pub) quando si assegnano le credenziali del cluster HDI:
      
		![Pannello Credenziali](./media/hdinsight-getting-started-with-r/publickeyfile.png)
      
	-    Modificare le autorizzazioni per il file di chiave privata sul computer portatile
      
			chmod 600 <private-key-filename>
      
	-    Utilizzare il file di chiave privata con SSH per l'accesso remoto, ad esempio
	  
			ssh –i <private-key-filename> remoteuser@<hostname public ip>
      
	  o come parte della definizione del contesto di calcolo di Hadoop Spark per R Server sul client (vedere la sezione Using Microsoft R Server as a Hadoop Client (Usare Microsoft R Server come un client Hadoop) nella sezione [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creazione di un contesto di calcolo per Spark) della [RevoScaleR Hadoop Spark Getting Started guide](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Guida introduttiva in linea RevoScaleR Hadoop Spark)).

7. Selezionare **Origine dati** per selezionare un'origine dati per il cluster. Selezionare un account di archiviazione esistente scegliendo __Selezionare l'account di archiviazione__ e quindi scegliere l'account oppure creare un nuovo account usando il link __Nuovo__ nella sezione __Selezionare l'account di archiviazione__.

    Se si seleziona __Nuovo__, è necessario immettere un nome per il nuovo account di archiviazione. Se il nome viene accettato, verrà visualizzato un segno di spunta verde.

    Il __Contenitore predefinito__ verrà reimpostato sul nome predefinito del cluster. Lasciare questo valore.
    
    Selezionare __Percorso__ per scegliere l'area in cui creare l'account di archiviazione.
    
    > [AZURE.IMPORTANT] La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

    Usare il pulsante **Seleziona** per salvare la configurazione dell'origine dati.
    
    ![Pannello di origine dati](./media/hdinsight-getting-started-with-r/datastore.png)

8. Selezionare **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. A meno che non si è consapevoli di aver bisogno di un cluster di maggiori dimensioni, lasciare il numero di nodi di lavoro sul valore predefinito di `4`. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	> [AZURE.NOTE] Se necessario, è possibile ridimensionare il cluster in un secondo momento tramite il portale (Cluster -> Impostazioni -> Scala Cluster) per aumentare o ridurre il numero di nodi di lavoro. Questo può essere utile per nascondere il cluster quando non è in uso o per aggiungere capacità al fine di soddisfare le esigenze delle attività più grandi.

	Ecco alcuni fattori da tenere presente quando si modificano le dimensioni del cluster, dei nodi di dati e del nodo perimetrale:
   
    - Quando la quantità di dati è ingente, le prestazioni delle analisi R Server distribuite in Spark sono proporzionali al numero di nodi di lavoro.
    - Le prestazioni delle analisi Server R sono proporzionali alle dimensioni dei dati analizzati. Ad esempio:
        - Per quantità di dati di piccole e medie dimensioni, le prestazioni sono migliori se l'analisi avviene in un contesto di calcolo locale sul nodo perimetrale. Per altre informazioni sugli scenari in cui i contesti di calcolo Spark e locale funzionano meglio, vedere Opzioni del contesto di calcolo per R Server su HDInsight.<br>
        - Se si accede al nodo perimetrale e si esegue lo script R, tutte le funzioni, ad eccezione delle funzioni ScaleR rx, verranno eseguite <strong>localmente</strong> sul nodo perimetrale in modo tale che la memoria e il numero di core del nodo perimetrale vengano ridimensionati secondo le esigenze. Lo stesso vale se si utilizza R Server su HDI come contesto di calcolo remoto dal computer portatile.
    
    ![Pannello livelli dei prezzi di nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Usare il pulsante **Seleziona** per salvare la configurazione dei piani tariffari del nodo.
    
9. Nel pannello **Nuovo cluster HDInsight** verificare che l'opzione **Pin to Startboard** (Aggiungi alla Schermata iniziale) sia selezionata e quindi scegliere **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al termine dell'operazione.

    | Durante la creazione | Creazione completata |
    | ------------------ | --------------------- |
    | ![Indicatore della creazione sulla schermata iniziale](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Riquadro del cluster creato](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.

## Connettersi al nodo perimetrale di R Server

Connettersi al nodo perimetrale di R Server del cluster HDInsight tramite SSH:

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] È anche possibile trovare l'indirizzo `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` nel portale di Azure selezionando il cluster e quindi __Tutte le impostazioni__, __App__ e __RServer__. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.
>
> ![Immagine dell'endpoint SSH per il nodo perimetrale](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`.
    
Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Una volta effettuata la connessione, verrà visualizzato un prompt come quello che segue.

    username@ed00-myrser:~$

## Usare la console di R

1. Nella sessione SSH usare il comando seguente per avviare la console R.

        R
    
    L'output sarà simile al seguente.
    
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

2. Dal prompt `>` è possibile immettere codice R. R Server include pacchetti che consentono di interagire facilmente con Hadoop ed eseguire calcoli distribuiti. Ad esempio, usare il comando seguente per visualizzare la root del file system predefinito per il cluster HDInsight.

        rxHadoopListFiles("/")
    
    È anche possibile usare l'indirizzamento in stile WASB.
    
        rxHadoopListFiles("wasbs:///")

## Utilizzare R Server in HDI da un'istanza remota di Microsoft R Server o Microsoft R Client

Per la sezione riportata sopra sull'uso delle coppie di chiavi pubblica/privata per accedere al cluster, è possibile configurare l'accesso al contesto di calcolo Hadoop Spark HDI da un'istanza remota di Microsoft R Server o Microsoft R Client in esecuzione su un computer desktop o portatile (vedere la sezione Using Microsoft R Server as a Hadoop Client (Usare Microsoft R Server come un client Hadoop) nella sezione [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creazione di un contesto di calcolo per Spark) della [RevoScaleR Hadoop Spark Getting Started guide](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Guida introduttiva in linea RevoScaleR Hadoop Spark)). A tale scopo è necessario specificare le opzioni seguenti quando si definisce il contesto di calcolo RxSpark sul computer portatile: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Ad esempio:

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
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

    
 
## Usare un contesto di calcolo

Un contesto di calcolo consente di controllare se il calcolo verrà eseguito localmente sul nodo perimetrale o se verrà distribuito sui nodi del cluster HDInsight.
        
1. Dalla console di R, usare quanto segue per caricare i dati di esempio nell'archivio predefinito per HDInsight.

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

    > [AZURE.NOTE] È anche possibile usare MapReduce per distribuire il calcolo sui nodi del cluster. Per altre informazioni sul contesto di calcolo, vedere [Opzioni del contesto di calcolo per R Server su HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).

## Distribuire il codice R su più nodi

Con R Server è possibile prelevare facilmente il codice R esistente ed eseguirlo su più nodi del cluster tramite `rxExec`. Questa operazione è utile quando si esegue lo sweep parametrico o le simulazioni. Di seguito è riportato un esempio su come usare `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Se si sta ancora usando il contesto Spark o MapReduce, verrà restituito il valore nodename per i nodi di lavoro su cui è in esecuzione il codice (`Sys.info()["nodename"]`). In un cluster a quattro nodi, ad esempio, è possibile ricevere un output simile al seguente.

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

## Installare pacchetti R

Per installare pacchetti R aggiuntivi nel nodo perimetrale, è possibile usare `install.packages()` direttamente dall'interno della console R quando si è connessi al nodo perimetrale tramite SSH. Tuttavia, se si desidera installare pacchetti R sui nodi di lavoro del cluster, è necessario usare un'azione di script.

Le azioni di script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight oppure per installare software aggiuntivo. In questo caso, viene usato per installare altri pacchetti R. Per installare altri pacchetti tramite un'azione di script, seguire questa procedura.

> [AZURE.IMPORTANT] Le azioni di script per installare altri pacchetti R sono possono essere usate solo dopo aver creato il cluster. Non è possibile usarle durante la creazione del cluster poiché lo script si basa su R Server completamente installato e configurato.

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il proprio R Server sul cluster HDInsight.

2. Dal pannello del cluster selezionare __Tutte le impostazioni__ e quindi __Azioni script__. Dal pannello __Azioni script__ selezionare __Invia nuova__ per inviare una nuova azione di script.

    ![Immagine del pannello Azioni script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Immettere le informazioni seguenti dal pannello __Invia azione script__.

  - __Nome__: un nome descrittivo per identificare lo script
  - __URI script Bash__: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
  - __Head__: questa opzione deve essere __deselezionata__
  - __Ruolo di lavoro__: questa opzione deve essere __selezionata__
  - __Zookeeper__: questa opzione deve essere __deselezionata__
  - __Parametri__: i pacchetti R da installare. Ad esempio, `bitops stringr arules`
  - __...__: questa opzione deve essere __selezionata__.

    > [AZURE.NOTE] 1. Per impostazione predefinita, tutti i pacchetti R vengono installati da uno snapshot dell'archivio MRAN di Microsoft coerente con la versione di R Server che è stato installato. L'installazione di versioni più recenti dei pacchetti espone al rischio di incompatibilità, tuttavia è possibile farlo specificando `useCRAN` come primo elemento dell'elenco del pacchetto, ad esempio `useCRAN bitops, stringr, arules`.
    > 2. Alcuni pacchetti R richiedono librerie di sistema di Linux aggiuntive. Per praticità sono state preinstallate le dipendenze necessarie per i 100 pacchetti R più diffusi. Tuttavia, se i pacchetti R installati richiedono altre librerie, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico su Archiviazione di Azure e usare lo script modificato per installare i pacchetti. Per altre informazioni sullo sviluppo di azioni script, vedere l'articolo [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

    ![Aggiunta di un'azione script](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Selezionare __Crea__ per eseguire lo script. Una volta completato lo script, i pacchetti R saranno disponibili su tutti i nodi di lavoro.
    
## Passaggi successivi

Dopo aver visto come creare un nuovo cluster HDInsight che include Server R e le nozioni di base sull'uso della console di R da una sessione SSH, usare le risorse seguenti per trovare altre modalità di utilizzo di Server R in HDInsight.

- [Articolo relativo all'installazione di RStudio Server in HDInsight Premium.](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opzioni del contesto di calcolo per R Server in HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni di Archiviazione di Azure per R Server in HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

### Modelli di Gestione risorse di Azure

Se si intende automatizzare la creazione di R Server in HDInsight tramite modelli di Gestione risorse di Azure, vedere i seguenti modelli di esempio.

* [Creare un R Server nel cluster HDInsight con una chiave pubblica SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Creare un R Server nel cluster HDInsight con una password SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Entrambi i modelli creano un nuovo cluster HDInsight e un account di archiviazione associato e possono essere usati dall'Interfaccia della riga di comando di Azure, da Azure PowerShell o dal portale di Azure.

Per informazioni generali sull'uso dei modelli di Azure Resource Manager, vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0921_2016-->