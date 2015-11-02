<properties
   	pageTitle="Creare cluster Hadoop, HBase o Storm in Linux in HDInsight tramite la CLI multipiattaforma di Azure | Microsoft Azure"
   	description="Informazioni su come creare cluster HDInsight basati su Linux tramite l’interfaccia multipiattaforma CLI di Azure, i modelli di gestione risorse di Azure e l'API REST di Azure. È possibile specificare il tipo di cluster (Hadoop, HBase o Storm) o utilizzare gli script per installare i componenti personalizzati."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/14/2015"
   	ms.author="larryfr"/>

#Creare cluster basati su Linux in HDInsight tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-linux-cluster-selector.md)]

L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di gestire i servizi di Azure. Può essere usata con i modelli di Gestione risorse di Azure per creare un cluster HDInsight insieme agli account di archiviazione associati e ad altri servizi.

I modelli di Gestione risorse di Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse in esso contenute, ad esempio HDInsight. Questo approccio basato sui modelli consente di definire tutte le risorse necessarie per HDInsight in un modello e di gestire le modifiche apportate all'intero gruppo mediante __distribuzioni__ che applicano modifiche al gruppo.

I passaggi illustrati in questo documento descrivono il processo di creazione di un nuovo cluster HDInsight tramite l'interfaccia della riga di comando di Azure e un modello:

##Prerequisiti

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Interfaccia della riga di comando di Azure__. Per informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).

##Effettuare l'accesso alla sottoscrizione di Azure

Seguire i passaggi descritti [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md) e connettersi alla sottoscrizione usando il metodo __login__.

##Creare un cluster

I seguenti passaggi devono essere eseguiti da un prompt dei comandi, una shell o una sessione terminal dopo l'installazione e la configurazione dell'interfaccia della riga di comando di Azure.

1. Usare il comando seguente per eseguire l'autenticazione della sottoscrizione di Azure:

        azure login

    Verrà chiesto di specificare il nome e la password. Se si dispone di più sottoscrizioni di Azure, è possibile usare `azure account set <subscriptionname>` per impostare la sottoscrizione che verrà usata dai comandi dell'interfaccia della riga di comando di Azure.

3. Passare alla modalità Gestione risorse di Azure usando il comando seguente:

        azure config mode arm

4. Creare un modello per il cluster HDInsight. Di seguito sono indicati alcuni modelli di esempio di base:

    * [Cluster basato su Linux, con una chiave pubblica SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [Cluster basato su Linux, con una password per l'account SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    Entrambi i modelli creano anche l'account di archiviazione di Azure predefinito usato da HDInsight.

    I file necessari sono __azuredeploy.json__ e __azuredeploy.parameters.json__. Copiare questi file in locale prima di continuare.

5. Aprire il file __azuredeploy.parameters.json__ in un editor e specificare i valori per gli elementi nella sezione `parameters`:

    * __location__: data center in cui verranno create le risorse. Per un elenco delle posizioni consentite, visualizzare la sezione `location` nel file __azuredeploy.json__.
    * __clusterName__: nome del cluster HDInsight. Questo nome deve essere univoco, altrimenti la distribuzione avrà esito negativo.
    * __clusterStorageAccountName__: nome dell'account di archiviazione di Azure che verrà creato per il cluster HDInsight. Questo nome deve essere univoco, altrimenti la distribuzione avrà esito negativo.
    * __clusterLoginPassword__: password dell'utente amministratore del cluster. Deve essere una password sicura, poiché viene usata per accedere ai siti Web e ai servizi REST nel cluster.
    * __sshUserName__: nome del primo utente SSH da creare per il cluster. Per accedere in remoto al cluster con questo account sarà necessario usare SSH. Il nome deve essere univoco e non può corrispondere al nome di un account già in uso nel cluster. I nomi non validi includono root, storm e hbase.
    * __sshPublicKey__: se si usa il modello che richiede una chiave pubblica SSH, è necessario aggiungere la chiave pubblica in questa riga. Per altre informazioni sulla generazione e sull'uso di chiavi pubbliche, vedere gli articoli seguenti:

        * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: se si usa il modello che richiede una password SSH, è necessario aggiungere una password in questa riga.

    Al termine, salvare e chiudere il file.

5. Per creare un gruppo di risorse vuoto, seguire questa procedura. Sostituire __RESOURCEGROUPNAME__ con il nome che si desidera usare per questo gruppo. Sostituire __LOCATION__ con il data center in cui si desidera creare il cluster HDInsight:

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Se il nome del percorso contiene spazi, racchiuderlo tra virgolette doppie, ad esempio "Stati Uniti centrali del sud".

6. Usare il comando seguente per creare la distribuzione iniziale per questo gruppo di risorse. Sostituire __PATHTOTEMPLATE__ con il percorso del file modello __azuredeploy.json__. Sostituire__PATHTOPARAMETERSFILE__ con il percorso del file __azuredeploy.parameters.json__. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo creato nel passaggio precedente:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Dopo che la distribuzione è stata accettata, verrà visualizzato un messaggio simile a `group deployment create command ok`.

7. Il completamento della distribuzione può richiedere tempo, circa 15 minuti. Usare il comando seguente per visualizzare informazioni sulla distribuzione. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse usato nel passaggio precedente:

        azure group log show -l RESOURCEGROUPNAME
    
    Una volta completata la distribuzione, il campo __Stato__ campo conterrà il valore __Operazione riuscita__. Se si verifica un errore durante la distribuzione, è possibile ottenere altre informazioni sull'errore usando il comando seguente:

        azure group log show -l -v RESOURCEGROUPNAME

##Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster:

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO4-->