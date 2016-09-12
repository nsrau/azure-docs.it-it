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
   	ms.date="08/30/2016"
   	ms.author="larryfr"/>  

#Creare cluster basati su Linux in HDInsight tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di gestire i servizi di Azure. Può essere usata con i modelli di Gestione risorse di Azure per creare un cluster HDInsight insieme agli account di archiviazione associati e ad altri servizi.

I modelli di Gestione risorse di Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse in esso contenute, ad esempio HDInsight. Questo approccio basato sui modelli consente di definire tutte le risorse necessarie per HDInsight in un modello e di gestire le modifiche apportate all'intero gruppo mediante __distribuzioni__ che applicano modifiche al gruppo.

I passaggi illustrati in questo documento descrivono il processo di creazione di un nuovo cluster HDInsight tramite l'interfaccia della riga di comando di Azure e un modello:

> [AZURE.IMPORTANT] I passaggi descritti in questo documento utilizzano il numero di nodi di lavoro predefinito (4) per un cluster HDInsight. Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Prerequisiti

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Interfaccia della riga di comando di Azure__. La procedura descritta in questo documento è stata testata con la versione dell'interfaccia della riga di comando di Azure 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Effettuare l'accesso alla sottoscrizione di Azure

Seguire i passaggi descritti in [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md) e connettersi alla sottoscrizione usando il metodo __login__.

##Creare un cluster

I seguenti passaggi devono essere eseguiti da un prompt dei comandi, una shell o una sessione terminal dopo l'installazione e la configurazione dell'interfaccia della riga di comando di Azure.

1. Per eseguire l'autenticazione della sottoscrizione di Azure, usare il comando seguente:

        azure login

    Verrà chiesto di specificare il nome e la password. Se si dispone di più sottoscrizioni di Azure, è possibile usare `azure account set <subscriptionname>` per impostare la sottoscrizione che verrà usata dai comandi dell'interfaccia della riga di comando di Azure.

3. Passare alla modalità Gestione risorse di Azure usando il comando seguente:

        azure config mode arm

4. Creare un nuovo gruppo di risorse. Questo gruppo contiene il cluster HDInsight e l'account di archiviazione associato.

        azure group create groupname location
        
    * Sostituire __groupname__ con un nome univoco per il gruppo.
    * Sostituire __location__ con l'area geografica in cui si vuole creare il gruppo.
    
        Per un elenco di località valide, usare il comando `azure locations list` e quindi una delle località della colonna __Nome__.

5. Creare un nuovo account di archiviazione. Questo account verrà usato come archivio predefinito per il cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Sostituire __groupname__ con il nome del gruppo creato nel passaggio precedente.
     * Sostituire __location__ con la stessa località usata nel passaggio precedente.
     * Sostituire __storagename__ con un nome univoco per l'account di archiviazione.
     
     > [AZURE.NOTE] Per altre informazioni sui parametri usati in questo comando, usare `azure storage account create -h` per visualizzare la Guida relativa a questo comando.

5. Recuperare la chiave usata per accedere all'account di archiviazione.

        azure storage account keys list -g groupname storagename
        
    * Sostituire __groupname__ con il nome del gruppo di risorse.
    * Sostituire __storagename__ con il nome dell'account di archiviazione.
    
    Nei dati restituiti salvare il valore __key__ per __key1__.

6. Creare un nuovo cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Sostituire __groupname__ con il nome del gruppo di risorse.
    * Sostituire __location__ con la stessa località usata nei passaggi precedenti.
    * Sostituire __storagename__ con il nome dell'account di archiviazione.
    * Sostituire __storagekey__ con la chiave ottenuta nel passaggio precedente.
    * Per il parametro `--defaultStorageContainer` usare lo stesso nome usato per il cluster.
    * Sostituire __admin__ e __httppassword__ con il nome e la password da usare per l'accesso al cluster tramite HTTPS.
    * Sostituire __sshuser__ e __sshuserpassword__ con il nome utente e la password da usare per l'accesso al cluster tramite SSH.

    Il processo di creazione del cluster può richiedere alcuni minuti, in genere circa 15.

##Passaggi successivi

Dopo aver creato un cluster HDInsight tramite l'interfaccia della riga di comando di Azure, usare le informazioni seguenti per acquisire familiarità con il cluster:

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0831_2016-->