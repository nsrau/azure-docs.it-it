<properties
   pageTitle="Introduzione a R Server su HDInsight (anteprima) | Azure"
   description="Informazioni sulla creazione di un cluster Apache Spark su HDInsight (anteprima) che include R Server e sull'invio di uno script R nel cluster."
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
   ms.date="03/25/2016"
   ms.author="jeffstok"/>

#Introduzione all'uso di R Server su HDInsight (anteprima)

L'offerta del piano Premium per HDInsight include R Server su HDInsight (anteprima). In questo modo gli script R possono usare MapReduce e Spark per eseguire i calcoli distribuiti. In questo documento si apprenderà come creare un nuovo R Server in HDInsight e come eseguire uno script R che illustra l'uso di Spark per i calcoli R distribuiti.

![Diagramma del flusso di lavoro del documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Prerequisiti

* __Una sottoscrizione ad Azure__: prima di iniziare questa esercitazione, è necessario disporre di una sottoscrizione ad Azure. Per altre informazioni, vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

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

3. Inserire il nome del cluster nel campo __Nome del cluster__. Se si dispone di più sottoscrizioni di Azure, usare la voce __Sottoscrizione__ per selezionare quella da usare.

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

    Immettere un __Nome utente SSH__ e selezionare __Password__, quindi immettere la __Password SSH__ per configurare l'account SSH. SSH è usato per connettersi al cluster da remoto tramite un client Secure Shell (SSH).
    
    Usare il pulsante __Seleziona__ per salvare le credenziali.
    
    ![Pannello Credenziali](./media/hdinsight-getting-started-with-r/clustercredentials.png)

7. Selezionare **Origine dati** per selezionare un'origine dati per il cluster. Selezionare un account di archiviazione esistente scegliendo __Selezionare l’account di archiviazione__ e quindi selezionare l'account oppure creare un nuovo account usando il link __Nuovo__ nella sezione __Selezionare l’account di archiviazione__.

    Se si seleziona __Nuovo__, è necessario immettere un nome per il nuovo account di archiviazione. Se il nome viene accettato, verrà visualizzato un segno di spunta verde.

    Il __Contenitore predefinito__ verrà reimpostato sul nome predefinito del cluster. Lasciare questo valore.
    
    Selezionare __Percorso__ per scegliere l'area in cui creare l'account di archiviazione.
    
    > [AZURE.IMPORTANT] La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

    Usare il pulsante **Seleziona** per salvare la configurazione dell'origine dati.
    
    ![Pannello di origine dati](./media/hdinsight-getting-started-with-r/datastore.png)

8. Selezionare **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. A meno che non si è consapevoli di aver bisogno di un cluster di maggiori dimensioni, lasciare il numero di nodi di lavoro sul valore predefinito di `4`. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

    ![Pannello livelli dei prezzi di nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Usare il pulsante **Seleziona** per salvare la configurazione dei piani tariffari del nodo.
    
9. Nel pannello **Nuovo cluster HDInsight** assicurarsi che l'opzione **Aggiungi alla Schermata iniziale** sia selezionata e quindi scegliere **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al termine dell'operazione.

    | Durante la creazione | Creazione completata |
    | ------------------ | --------------------- |
    | ![Indicatore della creazione sulla schermata iniziale](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Riquadro del cluster creato](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.

## Connettersi al nodo perimetrale di R Server

Connettersi al nodo perimetrale di R Server del cluster HDInsight tramite SSH:

    ssh USERNAME@rserver.CLUSTERNAME.ssh.azurehdinsight.net
    
> [AZURE.NOTE] È anche possibile trovare l'indirizzo `RServer.CLUSTERNAME.ssh.azurehdinsight.net` nel portale di Azure selezionando il cluster, quindi __Tutte le impostazioni__, __App__ e __RServer__. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.
>
> ![Immagine dell'endpoint SSH per il nodo perimetrale](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@RServer.CLUSTERNAME.ssh.azurehdinsight.net`.
    
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
    
        rxHadoopListFiles("wasb:///")

##Usare un contesto di calcolo

Un contesto di calcolo consente di controllare se il calcolo verrà eseguito localmente sul nodo perimetrale o se verrà distribuito sui nodi del cluster HDInsight.
        
1. Dalla console di R, usare quanto segue per caricare i dati di esempio nell'archivio predefinito per HDInsight.

        # Set the NameNode and port for the cluster
        myNameNode <- "default"
        myPort <- 0
        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # Source for the data to load
        source <- system.file("SampleData/AirlineDemoSmall.csv", package="RevoScaleR")
        # Directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirlineDemoSmall") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, inputDir)

2. Successivamente, si creano alcuni fattori e si definisce un'origine dati in modo da poter lavorare con i dati.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, 
                                   port=myPort)
        # Create Factors for the days of the week
        colInfo <- list(DayOfWeek = list(type = "factor",
             levels = c("Monday", 
                        "Tuesday", 
                        "Wednesday", 
                        "Thursday", 
                        "Friday", 
                        "Saturday", 
                        "Sunday")))
        # Define the data source
        airDS <- RxTextData(file = inputDir, 
                            missingValueString = "M",
                            colInfo  = colInfo, 
                            fileSystem = hdfsFS)

3. Provare a eseguire, quindi, una regressione lineare sui dati usando il contesto di calcolo locale.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a linear regression
        system.time(
            modelLocal <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek,
                                   data = airDS)
        )
        # Display a summary 
        summary(modelLocal) 

    L'output dovrebbe terminare con righe simili alle seguenti.
    
        Residual standard error: 40.39 on 582620 degrees of freedom
        Multiple R-squared: 0.01465
        Adjusted R-squared: 0.01464
        F-statistic:  1238 on 7 and 582620 DF,  p-value: < 2.2e-16
        Condition number: 10.6542

4. Quindi, provare a eseguire la stessa regressione lineare usando il contesto di Spark. Il contesto di Spark distribuirà l'elaborazione in tutti i nodi di lavoro del cluster HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark(consoleOutput=TRUE) 
        # Set the compute context 
        rxSetComputeContext(mySparkCluster) 
        # Run a linear regression 
        system.time(  
            modelSpark <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS) 
        )
        # Display a summary
        summary(modelSpark)

    L'output dell'elaborazione di Spark viene scritta nella console perché è impostato `consoleOutput=TRUE`.
    
    > [AZURE.NOTE] È anche possibile usare MapReduce per distribuire il calcolo sui nodi del cluster. Per altre informazioni sul contesto di calcolo, vedere [Opzioni del contesto di calcolo per R Server in HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

##Distribuire il codice R su più nodi

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

##Installare pacchetti R

Se si desidera installare altri pacchetti R sul nodo perimetrale, è possibile usare direttamente `install.packages()` dalla console interna di R quando si è connessi al nodo perimetrale tramite SSH. Tuttavia, se si desidera installare pacchetti R sui nodi di lavoro del cluster, è necessario usare un'azione di script.

Le azioni di script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight oppure per installare software aggiuntivo. In questo caso, viene usato per installare altri pacchetti R. Per installare altri pacchetti tramite un'azione di script, seguire questa procedura.

> [AZURE.IMPORTANT] Le azioni di script per installare altri pacchetti R sono possono essere usate solo dopo aver creato il cluster. Non è possibile usarle durante la creazione del cluster poiché lo script si basa su R Server completamente installato e configurato.

1. Nel [portale di Azure](https://portal.azure.com) selezionare il proprio R Server sul cluster HDInsight.

2. Dal pannello del cluster selezionare __Tutte le impostazioni__ e quindi __Azioni script__. Dal pannello __Azioni script__, selezionare __Invia nuova__ per inviare una nuova azione di script.

    ![Immagine del pannello Azioni script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Immettere le informazioni seguenti dal pannello __Invia azione di script__.

    * __Nome__: un nome descrittivo per identificare lo script
    * __URI di script Bash__: http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh
    * __Head__: questa opzione dovrebbe essere __deselezionata__
    * __Ruolo di lavoro__: questa opzione dovrebbe essere __selezionata__
    * __Zookeeper__: questa opzione dovrebbe essere __deselezionata__
    * __Parametri__: i pacchetti R da installare. Ad esempio, `bitops stringr arules`
    * __Continua questa azione di script...__: questa opzione dovrebbe essere __selezionata__
    
    > [AZURE.IMPORTANT] Se i pacchetti R installati richiedono l'aggiunta di librerie di sistema, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico su Archiviazione di Azure e usare lo script modificato per installare i pacchetti.
    >
    >Per altre informazioni su come sviluppare azioni di script, vedere [Sviluppo di azioni script](hdinsight-hadoop-script-actions-linux.md).
    
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

Per informazioni generali sull'uso dei modelli ARM, vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0420_2016-->