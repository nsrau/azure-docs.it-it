---
title: Introduzione ad Apache Storm in HDInsight | Microsoft Docs
description: Introduzione ad Apache Storm in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduzione ad Apache Storm in HDInsight: Analisi in tempo reale per Hadoop

Apache Storm in HDInsight consente di creare soluzioni di analisi distribuite e in tempo reale in Azure.

Apache Storm è un sistema di calcolo distribuito, a tolleranza di errore e open source che consente di elaborare i dati in tempo reale con Hadoop. Le soluzioni Storm possono fornire anche l'elaborazione garantita dei dati che consente di ripetere l'operazione per i dati non elaborati correttamente la prima volta.

## <a name="how-does-storm-work"></a>Funzionamento di Storm

Apache Storm elabora **topologie** al posto dei processi MapReduce con cui l'utente ha familiarità in HDInsight o Hadoop. Le topologie sono costituite da più componenti disposti in un grafo aciclico diretto (DAG). Il diagramma seguente illustra il flusso dei dati tra i componenti in una topologia di conteggio parole di base

![Esempio di disposizione dei componenti in una topologia Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* I componenti __spout__ inseriscono i dati in una topologia trasmettendo uno o più flussi nella topologia.

* I componenti __bolt__ utilizzano i flussi trasmessi dagli spout o da altri bolt. I bolt possono facoltativamente trasmettere nuovi flussi nella topologia. I bolt sono anche responsabili della scrittura dei dati in un archivio permanente, ad esempio HDFS o HBase.

## <a name="why-use-storm-on-hdinsight"></a>Vantaggi di Storm in HDInsight

Apache Storm in HDInsight offre i vantaggi chiave seguenti:

* Viene eseguito come servizio gestito con un contratto di servizio con disponibilità del 99,9%.

* Può essere personalizzato facilmente eseguendo script nel cluster durante o dopo la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

* Consente di usare il linguaggio di programmazione preferito perché i componenti Storm possono essere scritti in diversi linguaggi, ad esempio **Java**, **C#** e **Python**.

    * Integrazione di Visual Studio con HDInsight per lo sviluppo, la gestione e il monitoraggio di topologie C#. Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Supporta l'interfaccia Java **Trident**. Questa interfaccia consente di creare topologie Storm che supportano l'elaborazione di tipo exactly-once dei messaggi, la persistenza transazionale del datastore e un insieme di operazioni di analisi di flusso di uso comune.

* Aumenta e riduce le prestazioni del cluster: aggiunge o rimuove i nodi di lavoro senza impatto sull'esecuzione delle topologie Storm.

* Si integra con i servizi di Azure seguenti:

    * Hub eventi

    * Rete virtuale

    * Database SQL

    * Archiviazione di Azure

    * DocumentDB.

    * Combina in modo sicuro le funzionalità di più cluster HDInsight usando Rete virtuale di Azure. Crea pipeline di analisi che usano cluster HDInsight, HBase o Hadoop.

Per un elenco delle aziende che usano Apache Storm per le loro soluzioni di analisi in tempo reale, vedere l'articolo relativo alle [società che usano Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Per un'introduzione all'uso di Storm, vedere [Introduzione a Storm in HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Facilità di creazione

Il provisioning di un nuovo cluster Storm in HDInsight richiede solo alcuni minuti. Per altre informazioni sulla creazione di un cluster Storm, vedere [Introduzione a Storm in un HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Semplicità d'uso

* __Connettività Secure Shell__: è possibile accedere ai nodi head del cluster HDInsight tramite Internet usando SSH. SSH consente di eseguire i comandi direttamente nel cluster.

  Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Connettività Web__: i cluster HDInsight forniscono l'interfaccia utente Web Ambari, che consente di monitorare, configurare e gestire facilmente i servizi nel cluster. Storm in HDInsight fornisce anche l'interfaccia utente di Storm, che consente di monitorare e gestire le topologie Storm dal browser.

  Per altre informazioni, vedere [Gestire HDInsight usando l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md) e [Monitorare e gestire usando l'interfaccia utente di Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e interfaccia della riga di comando__: sia Azure PowerShell che l'interfaccia della riga di comando di Azure includono utilità della riga di comando che è possibile usare dal sistema client per lavorare con HDInsight e altri servizi di Azure.

* __Integrazione di Visual Studio__: Strumenti Azure Data Lake per Visual Studio include modelli di progetto per la creazione di topologie C# per Storm con il framework SCP.Net. Strumenti Data Lake include anche strumenti per distribuire, monitorare e gestire soluzioni con Storm in HDInsight.

  Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integrazione con altri servizi di Azure

* __Azure Data Lake Store__: per un esempio dell'uso di Data Lake Store con Storm, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Hub eventi di Azure__: per un esempio dell'uso di Hub eventi di Azure con Storm, vedere i documenti seguenti:

    * [Sviluppare una topologia di conteggio parole per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Gli esempi dell'uso di __SQL DB__, __DocumentDB__, __EventHub__ e __HBase__ sono inclusi come modelli in Azure Data Lake Tools per Visual Studio. Per altre informazioni, vedere [Sviluppare una topologia C# per Storm in HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Affidabilità

Apache Storm garantisce che ogni messaggio in arrivo verrà completamente elaborato, anche se l'analisi dei dati è distribuita tra centinaia di nodi.

Il **nodo Nimbus** offre funzionalità simili a quelle di JobTracker di Hadoop e assegna attività agli altri nodi del cluster tramite **Zookeeper**. I nodi Zookeeper assicurano la coordinazione del cluster e facilitano la comunicazione tra Nimbus e il processo **Supervisor** sui nodi di lavoro. Se un nodo di elaborazione si arresta, il nodo Nimbus riceve una notifica e provvede ad assegnare l'attività e i dati associati a un altro nodo.

La configurazione predefinita di Apache Storm prevede un solo nodo Nimbus. Storm in HDInsight esegue invece due nodi Nimbus. In caso di errore del nodo primario, il cluster HDInsight passerà a quello secondario. Nel frattempo, il nodo primario verrà ripristinato.

![Diagramma di Nimbus, Zookeeper e Supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Scalabilità

Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di modificare il numero di nodi, anche durante l'elaborazione dei dati.

> [!NOTE]
> Per sfruttare i vantaggi di nuovi nodi aggiunti tramite la scalabilità è necessario ribilanciare le topologie avviate prima dell'aumento delle dimensioni del cluster.

## <a name="support"></a>Supporto

Storm in HDInsight viene fornito con supporto completo di livello enterprise 24 ore su 24, 7 giorni su 7. Storm in HDInsight dispone inoltre di un contratto di servizio con disponibilità del 99,9%. Questo garantisce che il cluster avrà connettività esterna per almeno il 99,9% del tempo.

Per altre informazioni, vedere il [supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Casi d'uso comuni

Di seguito sono descritti alcuni scenari comuni in cui è possibile usare Apache Storm in HDInsight. 

* Internet delle cose
* Rilevamento delle frodi
* Analisi di social media
* ETL (Extract Transform Load)
* Monitoraggio della rete
* Search
* Mobile Engagement

Per informazioni sugli scenari reali, vedere il documento [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html) (Uso di Storm nelle aziende).

## <a name="development"></a>Sviluppo.

__Data Lake Tools per Visual Studio__ consente agli sviluppatori .NET di progettare e implementare una topologia in __C#__. È inoltre possibile creare topologie ibride che usano componenti Java e C#.

Per altre informazioni, vedere [Sviluppare topologie C# per Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

È anche possibile sviluppare soluzioni __Java__ usando l'IDE preferito. Per altre informazioni, vedere [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

Per sviluppare componenti Storm, è possibile usare anche Python. Per altre informazioni, vedere [Sviluppare topologie Storm usando Python in HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Modelli di sviluppo comuni

### <a name="guaranteed-message-processing"></a>Elaborazione garantita dei messaggi

Storm può fornire diversi livelli di elaborazione garantita dei messaggi. Un'applicazione Storm di base può ad esempio garantire un'elaborazione at-least-once, mentre Trident può garantire un'elaborazione exactly-once.

Per altre informazioni, vedere la sezione sulle [garanzie relative all'elaborazione dati](https://storm.apache.org/about/guarantees-data-processing.html) nel sito Web apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Il modello che prevede la lettura di una tupla di input, la generazione di nessuna o più tuple e l'inserimento della tupla di input subito dopo la fine del metodo di esecuzione è un modello comune. Storm mette a disposizione l'interfaccia [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) per automatizzarlo.

### <a name="joins"></a>Join

La modalità di unione dei flussi di dati varia a seconda delle applicazioni. È possibile, ad esempio, creare un join di ogni tupla da più flussi in un unico nuovo flusso oppure è possibile creare un solo join di più batch di tuple per una specifica finestra. In entrambi i casi il join può essere eseguito usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), che rappresenta un metodo per definire in che modo le tuple vengono indirizzate ai bolt.

Nell'esempio Java seguente, fieldsGrouping viene usato per indirizzare le tuple originate dai componenti "1", "2" e "3" al bolt **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Creazione di batch

Storm offre un meccanismo a tempo interno noto come "tupla tick", che può essere usato per generare un batch ogni X secondi.

Per un esempio dell'uso di una tupla tick da un componente C#, vedere [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Trident invece si basa sull'elaborazione di batch di tuple.

### <a name="caching"></a>Memorizzazione nella cache

La memorizzazione nella cache viene spesso usata come meccanismo per velocizzare l'elaborazione perché conserva in memoria le risorse usate di frequente. Poiché una topologia viene distribuita in più nodi e in più processi all'interno di ogni nodo, è consigliabile usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` garantisce che le tuple contenenti i campi usati per la ricerca nella cache vengano sempre indirizzate allo stesso processo. Questa funzionalità di raggruppamento evita la duplicazione delle voci della cache nei processi.

### <a name="streaming-top-n"></a>Streaming dei primi N elementi

Quando la topologia dipende dal calcolo di un valore "primi N", è consigliabile calcolare tale valore in parallelo. Unire quindi l'output di tali calcoli in un valore globale. Per eseguire questa operazione, usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) per l'indirizzamento per campo per l'elaborazione parallela e quindi eseguire l'indirizzamento a un bolt che determina globalmente il valore "primi N".

Per un esempio di calcolo di un valore "primi N", vedere [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registrazione

Storm usa Apache Log4j per registrare le informazioni. Per impostazione predefinita viene registrata una grande quantità di dati e può essere difficile esaminare le informazioni. È possibile includere un file di configurazione della registrazione nella topologia di Storm per definire il comportamento di registrazione.

Per un esempio di topologia che illustra come configurare la registrazione, vedere [Sviluppare topologie basate su Java per un'applicazione di base per il conteggio di parole con Apache Storm e Maven in HDInsight](hdinsight-storm-develop-java-topology.md) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle soluzioni di analisi in tempo reale con Apache Storm in HDInsight:

* [Introduzione a Storm in HDInsight][gettingstarted]
* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

