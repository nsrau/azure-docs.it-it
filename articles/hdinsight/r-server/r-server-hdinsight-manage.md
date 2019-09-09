---
title: Gestire cluster ML Services in Azure HDInsight - Azure
description: Informazioni su come gestire varie attività nel cluster di servizi ML in Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 568c4324f3b542e2b913596c1d93ffb72bbbaec7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814254"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Gestire cluster ML Services in Azure HDInsight

Questo articolo illustra come gestire un cluster di servizi di gestione delle risorse di Azure esistente in Azure HDInsight per eseguire attività quali l'aggiunta di più utenti simultanei, la connessione remota a un cluster di ML Services, la modifica del contesto di calcolo e così via.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster ML Services in HDInsight. Vedere [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **ML Services** per **Tipo di cluster**.


* Un client Secure Shell (SSH): il client SSH viene usato per connettersi da remoto al cluster HDInsight e per eseguire i comandi direttamente sul cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Abilitare più utenti simultanei

È possibile abilitare più utenti simultanei per un cluster ML Services in HDInsight aggiungendo altri utenti per il nodo perimetrale in cui viene eseguita la versione Community di RStudio. Quando si crea un cluster HDInsight, è necessario specificare due utenti: un utente HTTP e un utente SSH.

![Utenti simultanei 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nome utente dell'account di accesso del cluster**: utente HTTP per l'autenticazione tramite il gateway HDInsight usato per proteggere i cluster HDInsight creati. Questo utente HTTP viene usato per accedere all'interfaccia utente di Apache Ambari, all'interfaccia utente di Apache Hadoop YARN e ad altri componenti di interfaccia utente.
- **Nome utente Secure Shell (SSH)** : utente SSH per accedere al cluster tramite Secure Shell. È un utente del sistema Linux per tutti i nodi head, di lavoro e perimetrali. È così possibile usare Secure Shell per accedere a qualsiasi nodo di un cluster remoto.

La versione RStudio Server Community usata nel cluster ML Services in HDInsight accetta solo un nome utente e una password Linux come meccanismo di accesso. Non supporta il passaggio di token. Quando dunque si prova ad accedere a R Studio per la prima volta in un cluster ML Services, è necessario eseguire l'accesso due volte.

- Accedere prima con le credenziali utente HTTP tramite il gateway HDInsight. 

- Usare quindi le credenziali utente SSH per accedere a RStudio.
  
Attualmente, durante il provisioning di un cluster HDInsight è possibile creare un solo account utente SSH. Per consentire a più utenti di accedere a cluster ML Services in HDInsight, è necessario creare utenti aggiuntivi nel sistema Linux.

Poiché RStudio viene eseguito nel nodo perimetrale del cluster, si devono completare diversi passaggi:

1. Usare l'utente SSH esistente per accedere al nodo perimetrale
2. Aggiungere altri utenti Linux nel nodo perimetrale
3. Usare la versione Community di RStudio con l'utente creato

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Passaggio 1: Usare l'utente SSH creato per accedere al nodo perimetrale

Seguire le istruzioni riportate in [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) per accedere al nodo perimetrale. L'indirizzo del nodo perimetrale per il cluster ML Services in HDInsight è `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Passaggio 2: Aggiungere altri utenti Linux nel nodo perimetrale

Per aggiungere un utente al nodo perimetrale, eseguire questi comandi:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Lo screenshot seguente illustra gli output.

![Utenti simultanei 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Quando viene richiesto di immettere la password Kerberos corrente, è sufficiente premere **INVIO** per ignorare la richiesta. L'opzione `-m` nel comando `useradd` indica che il sistema creerà una home directory per l'utente, obbligatoria per la versione Community di RStudio.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Passaggio 3: Usare la versione Community di RStudio con l'utente creato

Accedere a RStudio da https://CLUSTERNAME.azurehdinsight.net/rstudio/. Se si sta eseguendo il primo accesso dopo la creazione del cluster, immettere le credenziali amministratore del cluster seguite dalle credenziali utente SSH create. Se non si tratta del primo accesso, immettere solo le credenziali per l'utente SSH create.

Si può anche accedere simultaneamente da un'altra finestra del browser con le credenziali originali (per impostazione predefinita, *sshuser*).

Si noti anche gli utenti appena aggiunti non hanno privilegi a livello radice nel sistema Linux, ma hanno lo stesso accesso a tutti i file nella risorsa di archiviazione HDFS e WASB remota.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Connettersi in modalità remota a Microsoft ML Services

È possibile configurare l'accesso al contesto di calcolo HDInsight Spark da un'istanza remota di ML Client in esecuzione nel computer desktop. A tale scopo, è necessario specificare le opzioni (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript) quando si definisce il contesto di calcolo RxSpark nel computer desktop: Ad esempio:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
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

Per altre informazioni, vedere la sezione "Using Microsoft Machine Learning Server as an Apache Hadoop Client" (Uso di Microsoft Machine Learning Server come client Apache Hadoop) in [How to use RevoScaleR in an Apache Spark compute context](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Come usare RevoScaleR in un contesto di calcolo per Apache Spark)

## <a name="use-a-compute-context"></a>Usare un contesto di calcolo

Un contesto di calcolo consente di controllare se il calcolo viene eseguito localmente sul nodo perimetrale o distribuito sui nodi del cluster HDInsight.  Per un esempio di impostazione di un contesto di calcolo con il server RStudio, vedere [eseguire uno script R in un cluster di servizi ml in Azure HDInsight usando rstudio server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuire il codice R su più nodi

Con ML Services in HDInsight, è possibile prelevare il codice R esistente ed eseguirlo su più nodi del cluster usando `rxExec`. Questa funzione è utile in caso di sweep di parametri o simulazioni. Il codice seguente è un esempio di come usare `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se si sta ancora usando il contesto Spark, questo comando restituisce il valore nodename per i nodi del ruolo di lavoro in cui viene eseguito il codice `(Sys.info()["nodename"])`. In un cluster a quattro nodi, ad esempio, verrà restituito un output simile al frammento seguente:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Accedere ai dati in Apache Hive e Parquet

HDInsight ML Services consente l'accesso diretto ai dati in Hive e Parquet per l'uso da parte delle funzioni di ScaleR nel contesto di calcolo di Spark. Queste funzionalità sono disponibili tramite nuove funzioni di origine dati ScaleR denominate RxHiveData e RxParquetData, che usano Spark SQL per caricare i dati direttamente in un frame di dati di Spark per l'analisi da parte di ScaleR.

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


Per altre informazioni sull'uso di queste nuove funzioni, vedere la guida online di ML Services tramite i comandi `?RxHivedata` e `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installare pacchetti R aggiuntivi nel cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Per installare pacchetti R nel nodo perimetrale

Per installare pacchetti R aggiuntivi nel nodo perimetrale, è possibile usare `install.packages()` direttamente dall'interno della console R, dopo essersi connessi al nodo perimetrale tramite SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Per installare pacchetti R nel nodo di lavoro

Per installare pacchetti R nei nodi di lavoro del cluster, è necessario usare un'azione script. Le azioni di script sono script Bash usati per apportare modifiche di configurazione al cluster HDInsight o per installare software aggiuntivo, ad esempio altri pacchetti R. 

> [!IMPORTANT]  
> Le azioni di script per installare altri pacchetti R sono possono essere usate solo dopo aver creato il cluster. Non usare questa procedura durante la creazione del cluster perché lo script si basa su ML Services completamente configurato.

1. Seguire i passaggi in [Personalizzare i cluster con l'azione script](../hdinsight-hadoop-customize-cluster-linux.md).

3. Immettere le informazioni seguenti per **Invia azione script**:

   * Per **Tipo di script** selezionare **Personalizzato**.

   * Per **Nome** specificare un nome per l'azione script.

     * Per **URI script Bash** immettere `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Questo è lo script che installa i pacchetti R aggiuntivi nel nodo di lavoro

   * Selezionare la casella di controllo solo per **Lavoro**.

   * **Parametri**: i pacchetti R da installare. Ad esempio: `bitops stringr arules`

   * Selezionare la casella di controllo **Persist this script action** (Salva questa azione script in modo permanente).  

   > [!NOTE]
   > 1. Per impostazione predefinita, tutti i pacchetti R vengono installati da uno snapshot dell'archivio MRAN di Microsoft coerente con la versione di ML Server che è stata installata. L'installazione di versioni più recenti dei pacchetti espone al rischio di incompatibilità. Questo tipo di installazione è tuttavia possibile specificando `useCRAN` come primo elemento dell'elenco dei pacchetti, ad esempio `useCRAN bitops, stringr, arules`.  
   > 2. Alcuni pacchetti R richiedono librerie di sistema di Linux aggiuntive. Per praticità in HDInsight ML Services sono state preinstallate le dipendenze necessarie per i 100 pacchetti R più diffusi. Tuttavia, se i pacchetti R installati richiedono altre librerie, è necessario scaricare lo script di base usato qui e continuare la procedura per installare le librerie di sistema. È quindi necessario caricare lo script modificato in un contenitore BLOB pubblico su Archiviazione di Azure e usare lo script modificato per installare i pacchetti.
   >    Per altre informazioni sullo sviluppo di azioni script, vedere l'articolo [Sviluppo di azioni script con HDInsight](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Aggiunta di un'azione script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Selezionare **Crea** per eseguire lo script. Una volta completato lo script, i pacchetti R sono disponibili su tutti i nodi del ruolo di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* [Rendere operativo un cluster ML Services in Azure HDInsight](r-server-operationalize.md)
* [Opzioni del contesto di calcolo per il cluster ML Services in HDInsight](r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per un cluster ML Services su HDInsight](r-server-storage.md)
