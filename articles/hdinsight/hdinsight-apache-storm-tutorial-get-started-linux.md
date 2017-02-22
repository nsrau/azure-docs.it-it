---
title: Introduzione ad Apache Storm in Azure HDInsight | Documentazione Microsoft
description: Introduzione all&quot;analisi di Big Data mediante Apache Storm ed esempi di Storm Starter in HDInsight basato su Linux. Informazioni su come usare Storm per elaborare i dati in tempo reale.
keywords: apache storm,esercitazione su apache storm,analisi Big Data,storm starter
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 9b38cd0aa542c0fd73b73edefce230e5a463e608


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Esercitazione di Apache Storm: Introduzione all'analisi di Big Data mediante esempi di Storm Starter su HDInsight

Apache Storm è un sistema di calcolo in tempo reale scalabile, a tolleranza di errore e distribuito per l'elaborazione di flussi di dati. Con Storm in Azure HDInsight è possibile creare un cluster Storm basato sul cloud che esegue analisi di Big Data in tempo reale.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per completare correttamente questa esercitazione di Apache Storm, è necessario quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:
  
    * **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Creare un cluster di Storm

In questa sezione si crea un cluster HDInsight versione 3.5 (Storm versione 1.0.1) usando un modello di Azure Resource Manager. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md). Per altri metodi di creazione dei cluster, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello si trova in un contenitore BLOB pubblico, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 

2. Compilare i campi seguenti del pannello __Distribuzione personalizzata__:
   
    * __Gruppo di risorse__: il gruppo di risorse nel quale viene creato il cluster.

    * **Nome cluster**: il nome del cluster Hadoop.

    * __Nome utente__ e __password di accesso del cluster__: il nome di accesso predefinito è admin.
    
    * __Nome utente SSH__ e __password__: l'utente e la password per connettersi al cluster tramite SSH.

    * __Posizione__: la posizione geografica del cluster.
     
     Annotare questi valori.  Sarà necessario utilizzarli più avanti nell'esercitazione.
     
     > [!NOTE]
     > Il protocollo SSH viene usato per accedere in modalità remota al cluster HDInsight tramite una riga di comando. Il nome utente e la password specificati qui sono usati per la connessione al cluster tramite SSH. Il nome utente SSH deve essere univoco, in quanto crea un account utente in tutti i nodi del cluster HDInsight. Di seguito sono riportati alcuni nomi di account riservati per l'uso da parte dei servizi nel cluster che non devono essere usati come nome utente SSH:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:
     > 
     > * [Usare SSH con Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Selezionare __Accetto le condizioni riportate sopra__ e fare clic su **OK**, quindi selezionare __Aggiungi al dashboard__.

6. Fare clic su **Acquista**. Verrà visualizzato un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione di un cluster richiede circa 20 minuti.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Eseguire un esempio Storm Starter in HDInsight

Gli esempi di [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) sono inclusi nel cluster HDInsight. Nei passaggi seguenti, verrà eseguito l'esempio WordCount.

1. Connettersi al cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:
   
    * [Usare SSH con Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Usare il comando seguente per avviare una topologia di esempio:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > Nelle versioni precedenti di HDInsight, il nome della classe della topologia è `storm.starter.WordCountTopology` anziché `org.apache.storm.starter.WordCountTopology`.
   
    Verrà avviata la topologia di esempio WordCount nel cluster usando 'wordcount' come nome descrittivo. Verranno generate in modo casuale le frasi e verranno conteggiate le occorrenze di ogni parola nelle frasi.
   
    > [!NOTE]
    > Durante l'invio di una topologia al cluster, è prima di tutto necessario copiare il file con estensione jar contenente il cluster prima di usare il comando `storm`. A tale scopo, usare il comando `scp` dal client in cui è presente il file. Ad esempio: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > L'esempio WordCount e altri esempi di avvio dell'utilità storm sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Se si è interessati a visualizzare il codice sorgente per gli esempi di Storm Starter, è possibile trovare il codice all'indirizzo [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Questo collegamento è per Storm 1.0, che viene offerto con HDInsight 3.5. Per altre versioni di Storm, usare il pulsante __Branch__ nella parte superiore della pagina per selezionare una versione diversa di Storm.

## <a name="monitor-the-topology"></a>Monitorare la topologia

L'interfaccia utente di Storm è inclusa nel cluster HDInsight e fornisce un'interfaccia Web da usare con le topologie in esecuzione.

Usare la procedura seguente per monitorare la topologia con l'interfaccia utente Storm:

1. Aprire un Web browser e accedere all'indirizzo https://CLUSTERNAME.azurehdinsight.net/stormui, dove **CLUSTERNAME** corrisponde al nome del cluster. Verrà aperta l'interfaccia utente Storm.
    
    > [!NOTE]
    > Se viene richiesto di fornire un nome utente e una password, immettere l'amministratore del cluster e la password usati durante la creazione del cluster.

2. Nella sezione **Topology summary** (Riepilogo topologie) selezionare la voce **wordcount** (conteggio parole) nella colonna **Nome**. Verranno visualizzate altre informazioni sulla topologia.
    
    ![Storm Dashboard con informazioni sulla topologia di Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    In questa pagina sono disponibili le informazioni seguenti:
    
    * **Topology stats** : informazioni di base sulle prestazioni della topologia, organizzate in intervalli di tempo.
     
        > [!NOTE]
        > La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.

    * **Spouts** : informazioni di base sugli spout, incluso l'ultimo errore restituito da ciascuno di essi.
    
    * **Bolts** : informazioni di base sui bolt.
    
    * **Topology configuration** : informazioni dettagliate sulla configurazione della topologia.
     
    Questa pagina fornisce anche azioni che possono essere eseguite sulla topologia:
   
    * **Activate** : riprende l'elaborazione di una topologia disattivata.
    
    * **Deactivate** : sospende una topologia in esecuzione.
    
    * **Rebalance** : regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster. Per altre informazioni, vedere l'articolo relativo al [parallelismo di una topologia Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Kill** : arresta una topologia Storm dopo il timeout specificato.

3. In questa pagina selezionare una voce nella sezione **Spouts** o **Bolts**. Verranno visualizzate informazioni relative al componente selezionato.
   
    ![Storm Dashboard con informazioni sui componenti selezionati.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    In questa pagina vengono visualizzate le informazioni seguenti:
   
    * **Spout/Bolt stats** : informazioni di base sulle prestazioni, organizzate in intervalli di tempo.
     
        > [!NOTE]
        > La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.
     
    * **Input stats** (solo bolt): informazioni sui componenti che generano dati utilizzati dal bolt.
    
    * **Output stats** : informazioni sui dati generati dal bolt.
    
    * **Executors** : informazioni sulle istanze del componente.
    
    * **Errors** : errori generati dal componente.

4. Quando si visualizzano i dettagli di uno spout o di un bolt, selezionare una voce nella colonna **Porta** della sezione **Esecutori** per visualizzare i dettagli relativi a una specifica istanza del componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    Da questi dati è possibile vedere che la parola **seven** è stata rilevata 1493957 volte. In altri termini, il numero&14939;57 indica le occorrenze della parola dall'avvio della topologia.

## <a name="stop-the-topology"></a>Arrestare la topologia

Tornare alla pagina **Riepilogo topologie** per la topologia relativa al conteggio delle parole e quindi fare clic sul pulsante **Kill** dalla sezione **Topology actions** (Azioni di topologia). Quando richiesto, immettere 10 per il numero di secondi di attesa prima dell'arresto della topologia. Dopo il periodo di timeout, la topologia non verrà più visualizzata nella sezione **Storm UI** del dashboard.

## <a name="delete-the-cluster"></a>Eliminazione del cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>Passaggi successivi

In questa esercitazione di Apache Storm si è usato Storm Starter per apprendere come creare un cluster Storm in HDInsight e usare Storm Dashboard per distribuire, monitorare e gestire topologie Storm. Vedere quindi altre informazioni su come [sviluppare topologie basate su Java con Maven](hdinsight-storm-develop-java-topology.md).

Se si ha già familiarità con lo sviluppo di topologie basate su Java e si vuole distribuire una topologia esistente in HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

Uno sviluppatore .NET può creare le topologie C# o C#/Java ibride con Virtual Studio. Per altre informazioni, vedere [Sviluppare topologie C# per Apache Storm in HDInsight con gli strumenti Hadoop per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Di seguito sono riportati alcuni esempi delle topologie che si possono usare con Storm in HDInsight:

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Feb17_HO3-->


