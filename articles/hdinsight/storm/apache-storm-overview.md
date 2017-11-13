---
title: Informazioni su Apache Storm in Azure HDInsight | Microsoft Docs
description: "Apache Storm permette di elaborare flussi di dati in tempo reale. Azure HDInsight permette di creare facilmente cluster Storm nel cloud di Azure. Con Visual Studio, è possibile creare soluzioni Storm con C# e poi distribuirle nei cluster Storm in HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: casi d'uso di Apache Storm,cluster Storm,informazioni su Apache Storm
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2017
ms.author: larryfr
ms.openlocfilehash: c978a9ba97ecb9b8facaf32cbefbdd06cab8df67
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Che cos'è Apache Storm in Azure HDInsight?

[Apache Storm](http://storm.apache.org/) è un sistema di calcolo distribuito, a tolleranza di errore e open source. È possibile usare Storm per elaborare i flussi di dati in tempo reale con Hadoop. Le soluzioni Storm possono fornire anche l'elaborazione garantita dei dati che consente di ripetere l'operazione per i dati non elaborati correttamente la prima volta.

Storm in HDInsight offre i vantaggi chiave seguenti:

* Viene eseguito come servizio gestito con un contratto di servizio con tempo di attività del 99,9%.

* Può essere personalizzato facilmente eseguendo script nel cluster Storm durante o dopo la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante l'azione script](../hdinsight-hadoop-customize-cluster-linux.md).

* Usa diversi linguaggi. È possibile scrivere i componenti Storm nel linguaggio preferito, ad esempio Java, C# e Python.

    * Integra Visual Studio con HDInsight per lo sviluppo, la gestione e il monitoraggio di topologie C#. Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Supporta l'interfaccia Java Trident. È possibile creare topologie Storm che supportano l'elaborazione di tipo exactly-once dei messaggi, la persistenza transazionale del datastore e un insieme di operazioni di analisi di flusso di uso comune.

*  È possibile aumentare o ridurre facilmente le prestazioni dei cluster Storm. È possibile aggiungere o rimuovere i nodi di lavoro senza impatto sull'esecuzione delle topologie Storm.

* Si integra con i servizi di Azure seguenti:

    * Hub eventi di Azure

    * Rete virtuale di Azure

    * Database SQL di Azure

    * Archiviazione di Azure

    * Azure Cosmos DB

* Combina in modo sicuro le funzionalità di più cluster HDInsight usando la rete virtuale. È possibile creare pipeline di analisi che usano cluster Storm, Kafka, Spark, HBase o Hadoop.

Per un elenco delle società che usano Apache Storm per le loro soluzioni di analisi in tempo reale, vedere l'articolo relativo alle [società che usano Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Per un'introduzione all'uso di Storm, vedere [Introduzione a Storm in HDInsight][gettingstarted].

## <a name="how-does-storm-work"></a>Funzionamento di Storm

Storm esegue topologie al posto dei processi MapReduce con cui l'utente ha familiarità. Le topologie Storm sono costituite da più componenti disposti in un grafo aciclico diretto (DAG). I componenti del grafo scambiano flussi di dati. Ogni componente utilizza uno o più flussi di dati e, facoltativamente, trasmette uno o più flussi. Il diagramma seguente illustra il flusso dei dati tra i componenti in una topologia di conteggio parole di base:

![Esempio di disposizione dei componenti in una topologia Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* I componenti spout inseriscono i dati in una topologia trasmettendo uno o più flussi nella topologia stessa.

* I componenti bolt usano i flussi trasmessi dagli spout o da altri bolt. Facoltativamente, i bolt possono trasmettere flussi nella topologia. I bolt sono anche responsabili della scrittura dei dati in un archivio o servizio esterno, ad esempio HDFS, Kafka o HBase.

## <a name="ease-of-creation"></a>Facilità di creazione

È possibile creare un nuovo cluster Storm in HDInsight in pochi minuti. Per altre informazioni sulla creazione di un cluster Storm, vedere [Introduzione a Storm in un HDInsight](apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Semplicità d'uso

* __Connettività Secure Shell (SSH)__: è possibile accedere ai nodi head del cluster Storm tramite Internet usando SSH. È possibile eseguire comandi direttamente nel cluster tramite SSH.

  Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Connettività Web__: tutti i cluster HDInsight offrono l'interfaccia utente Web Ambari, che consente di monitorare, configurare e gestire facilmente i servizi nel cluster tramite l'interfaccia utente Web Ambari. I cluster Storm offrono anche l'interfaccia utente di Storm. che consente di monitorare e gestire le topologie Storm in esecuzione dal browser.

  Per altre informazioni, vedere [Gestire HDInsight usando l'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md) e [Monitorare e gestire usando l'interfaccia utente di Storm](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e interfaccia della riga di comando di Azure__: sia Azure PowerShell che l'interfaccia della riga di comando includono utilità della riga di comando che è possibile usare dal sistema client per operazioni con HDInsight e altri servizi di Azure.

* __Integrazione di Visual Studio__: Strumenti Azure Data Lake per Visual Studio include modelli di progetto per la creazione di topologie Storm C# con il framework SCP.Net. Strumenti Data Lake include anche strumenti per distribuire, monitorare e gestire soluzioni con Storm in HDInsight.

  Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integrazione con altri servizi di Azure

* __Azure Data Lake Store__: per un esempio dell'uso di Data Lake Store con un cluster Storm, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](apache-storm-write-data-lake-store.md).

* __Hub eventi__: per un esempio dell'uso di Hub eventi con un cluster Storm, vedere gli esempi seguenti:

    * [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Database SQL__, __Cosmos DB__, __Hub eventi__, e __HBase__: Strumenti Azure Data Lake per Visual Studio include esempi di modelli. Per altre informazioni, vedere [Sviluppare una topologia C# per Storm in HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Affidabilità

Apache Storm garantisce l'elaborazione completa di ogni messaggio in arrivo, anche se l'analisi dei dati è distribuita tra centinaia di nodi.

Il nodo Nimbus offre funzionalità simili a quelle di JobTracker di Hadoop e assegna attività agli altri nodi del cluster tramite Zookeeper. I nodi Zookeeper assicurano la coordinazione del cluster e consentono la comunicazione tra Nimbus e il processo Supervisor nei nodi di lavoro. Se un nodo di elaborazione si arresta, il nodo Nimbus riceve una notifica e provvede ad assegnare l'attività e i dati associati a un altro nodo.

La configurazione predefinita per i cluster Apache Storm prevede un solo nodo Nimbus. Storm in HDInsight prevede invece due nodi Nimbus. In caso di errore del nodo primario, il cluster Storm passa a quello secondario mentre il nodo primario viene ripristinato. Il diagramma seguente illustra la configurazione del flusso di attività per Storm in HDInsight:

![Diagramma di Nimbus, Zookeeper e Supervisor](./media/apache-storm-overview/nimbus.png)

## <a name="scale"></a>Scalabilità

È possibile ridimensionare in modo dinamico i cluster HDInsight aggiungendo o rimuovendo nodi del ruolo di lavoro. L'operazione può essere eseguita durante l'elaborazione dei dati.

> [!IMPORTANT]
> Per sfruttare i vantaggi di nuovi nodi aggiunti tramite la scalabilità è necessario ribilanciare le topologie Storm avviate prima dell'aumento delle dimensioni del cluster.

## <a name="support"></a>Supporto

Storm in HDInsight viene fornito con supporto continuo di livello aziendale. Storm in HDInsight offre anche un contratto di servizio con disponibilità del 99,9%. Questo significa che Microsoft garantisce che un cluster Storm avrà connettività esterna per almeno il 99,9% del tempo.

Per altre informazioni, vedere il [supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Casi d'uso di Apache Storm

Di seguito sono descritti alcuni scenari comuni in cui è possibile usare Storm in HDInsight:

* Internet delle cose
* Rilevamento delle frodi
* Analisi di social media
* Estrazione, trasformazione e caricamento (ETL)
* Monitoraggio della rete
* Search
* Mobile Engagement

Per informazioni sugli scenari reali, vedere il documento [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html) (Uso di Storm nelle aziende).

## <a name="development"></a>Sviluppo.

Gli sviluppatori .NET possono progettare e implementare topologie in C# usando Strumenti Azure Data Lake per Visual Studio. È inoltre possibile creare topologie ibride che usano componenti Java e C#.

Per altre informazioni, vedere [Sviluppare topologie C# per Storm in HDInsight tramite Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

È anche possibile sviluppare soluzioni Java usando l'IDE preferito. Per altre informazioni, vedere [Sviluppare topologie Java per Storm in HDInsight](apache-storm-develop-java-topology.md).

Per sviluppare componenti Storm, è possibile usare anche Python. Per altre informazioni, vedere [Sviluppare topologie Storm usando Python in HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Modelli di sviluppo comuni

### <a name="guaranteed-message-processing"></a>Elaborazione garantita dei messaggi

Apache Storm può offrire diversi livelli di elaborazione garantita dei messaggi. Un'applicazione Storm di base può ad esempio garantire un'elaborazione at-least-once, mentre Trident può garantire un'elaborazione exactly-once.

Per altre informazioni, vedere la sezione sulle [garanzie relative all'elaborazione dati](https://storm.apache.org/about/guarantees-data-processing.html) nel sito Web apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Il modello che prevede la lettura di una tupla di input, la generazione di nessuna o più tuple e l'inserimento della tupla di input subito dopo la fine del metodo di esecuzione è un modello comune. Storm mette a disposizione l'interfaccia [IBasicBolt](https://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/topology/IBasicBolt.html) per automatizzarlo.

### <a name="joins"></a>Join

La modalità di unione dei flussi di dati varia a seconda delle applicazioni. È possibile, ad esempio, creare un join di ogni tupla da più flussi in un unico nuovo flusso oppure è possibile creare un solo join di più batch di tuple per una specifica finestra. In entrambi i casi è possibile creare un join tramite [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Il raggruppamento dei campi permette di definire il modo in cui le tuple vengono indirizzate ai bolt.

Nell'esempio Java seguente, fieldsGrouping viene usato per indirizzare le tuple originate dai componenti "1", "2" e "3" al bolt MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Batch

Apache Storm include un meccanismo a tempo interno noto come "tupla tick". È possibile impostare la frequenza con cui viene trasmessa una tupla tick nella topologia.

Per un esempio dell'uso di una tupla tick da un componente C#, vedere [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Cache

La memorizzazione nella cache viene spesso usata come meccanismo per velocizzare l'elaborazione perché conserva in memoria le risorse usate di frequente. Poiché una topologia viene distribuita in più nodi e in più processi all'interno di ogni nodo, è consigliabile usare [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Usare `fieldsGrouping` per garantire che le tuple contenenti i campi usati per la ricerca nella cache vengano sempre indirizzate allo stesso processo. Questa funzionalità di raggruppamento evita la duplicazione delle voci della cache nei processi.

### <a name="stream-top-n"></a>Flusso di "primi N"

Quando la topologia dipende dal calcolo di un valore "primi N", calcolare tale valore in parallelo. Unire quindi l'output di tali calcoli in un valore globale. A tale scopo, è possibile usare [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) per l'indirizzamento per campo per l'elaborazione parallela. Quindi eseguire l'indirizzamento a un bolt che determina globalmente il valore "primi N".

Per un esempio di calcolo di un valore "primi N", vedere [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registrazione

Storm usa Apache Log4j per registrare le informazioni. Per impostazione predefinita viene registrata una grande quantità di dati e può essere difficile esaminare le informazioni. È possibile includere un file di configurazione della registrazione nella topologia di Storm per definire il comportamento di registrazione.

Per un esempio di topologia che illustra come configurare la registrazione, vedere [Sviluppare topologie basate su Java per un'applicazione di base per il conteggio di parole con Apache Storm e Maven in HDInsight](apache-storm-develop-java-topology.md) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle soluzioni di analisi in tempo reale con Storm in HDInsight:

* [Introduzione ad Apache Storm in HDInsight][gettingstarted]
* [Topologie di esempio per Apache Storm in HDInsight](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
