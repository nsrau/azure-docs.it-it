---
title: Introduzione ad Apache Storm in HDInsight | Microsoft Docs
description: Viene fornita un&quot;introduzione ad Apache Storm e viene mostrato come usare Storm in HDInsight per creare soluzioni di analisi in tempo reale nel cloud.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: 426c7f5860a4be45b4c8e7ff4dd4b4406715ea34


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduzione ad Apache Storm in HDInsight: Analisi in tempo reale per Hadoop

Apache Storm in HDInsight consente di creare soluzioni di analisi distribuite e in tempo reale nell'ambiente Azure usando [Apache Hadoop](http://hadoop.apache.org).

## <a name="what-is-apache-storm"></a>Che cos'è Apache Storm?

Apache Storm è un sistema di calcolo distribuito, a tolleranza di errore e open source che consente di elaborare i dati in tempo reale con Hadoop. Le soluzioni Storm possono fornire anche l'elaborazione garantita dei dati che consente di ripetere l'operazione per i dati non elaborati correttamente la prima volta.

## <a name="why-use-storm-on-hdinsight"></a>Perché usare Storm in HDInsight?

Apache Storm in HDInsight è un cluster gestito integrato nell'ambiente Azure Storm e gli altri componenti di Hadoop in HDInsight si basano su Hortonworks Data Platform (HDP), mentre il sistema operativo per il cluster è Ubuntu (una distribuzione Linux). La piattaforma offerta è quindi estremamente compatibile con strumenti e servizi popolari nell'ecosistema Hadoop.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

Apache Storm in HDInsight offre i vantaggi chiave seguenti:

* Viene eseguito come servizio gestito con un contratto di servizio con disponibilità del 99,9%.

* Può essere personalizzato facilmente eseguendo script nel cluster durante o dopo la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

* Consente di usare il linguaggio di programmazione preferito. Offre infatti supporto per componenti Storm scritti in **Java**, **C#** e **Python**.
  
  * Integrazione di Visual Studio con HDInsight per lo sviluppo, la gestione e il monitoraggio di topologie C#. Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

  * Supporta l'interfaccia Java **Trident**. Questa interfaccia consente di creare topologie Storm che supportano l'elaborazione di tipo "exactly-once" dei messaggi, la persistenza "transazionale" del datastore e un insieme di operazioni di analisi di flusso di uso comune.

* Aumenta e riduce le prestazioni del cluster: aggiunge o rimuove i nodi di lavoro senza impatto sull'esecuzione delle topologie Storm.

* È integrato con altri servizi di Azure, inclusi Hub eventi, Rete virtuale di Azure, Database SQL, Archiviazione BLOB e DocumentDB.
  
  * Combina in modo sicuro le funzionalità di più cluster HDInsight usando Rete virtuale di Azure. Crea pipeline di analisi che usano cluster HDInsight, HBase o Hadoop.

Per un elenco delle aziende che usano Apache Storm per le loro soluzioni di analisi in tempo reale, vedere l'articolo relativo alle [società che usano Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Per un'introduzione all'uso di Storm, vedere [Introduzione a Storm in HDInsight][gettingstarted].

### <a name="ease-of-provisioning"></a>Facilità di provisioning

Il provisioning di un nuovo cluster Storm in HDInsight richiede solo alcuni minuti. Specificare il nome, le dimensioni, l'account amministratore e l'account di archiviazione del cluster. Azure creerà il cluster, inclusi topologie di esempio e un dashboard di gestione Web.

> [!NOTE]
> È anche possibile effettuare il provisioning di cluster Storm usando l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) o [Azure PowerShell](/powershell/azureps-cmdlets-docs).

Entro 15 minuti dall'invio della richiesta il nuovo cluster Storm sarà in esecuzione, pronto ad elaborare la prima pipeline di analisi in tempo reale.

### <a name="ease-of-use"></a>Semplicità d'uso

* __Connettività Secure Shell__: è possibile accedere ai nodi head del cluster HDInsight tramite Internet usando SSH. In questo modo è possibile eseguire i comandi direttamente nel cluster.

  Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Connettività Web__: i cluster HDInsight forniscono l'interfaccia utente Web Ambari, che consente di monitorare, configurare e gestire facilmente i servizi nel cluster. Storm in HDInsight fornisce anche l'interfaccia utente di Storm, che consente di monitorare e gestire le topologie Storm dal browser.

  Per altre informazioni, vedere [Gestire HDInsight usando l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md) e [Monitorare e gestire usando l'interfaccia utente di Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-using-the-storm-ui).

* __Azure PowerShell e interfaccia della riga di comando__: sia Azure PowerShell che l'interfaccia della riga di comando di Azure includono utilità della riga di comando che è possibile usare dal sistema client per lavorare con HDInsight e altri servizi di Azure.

* __Integrazione di Visual Studio__: Data Lake Tools per Visual Studio (noto in precedenza come strumenti di HDInsight per Visual Studio) include modelli di progetto per la creazione di topologie C# per Storm, oltre agli strumenti per Storm in HDInsight. È possibile creare, distribuire e gestire le topologie C# da Visual Studio.

  Per altre informazioni, vedere [Sviluppare topologie C# per Storm con gli strumenti di HDInsight per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

* __Integrazione con altri servizi di Azure__

  * Per lo sviluppo __Java__, Microsoft sfrutta i componenti Storm esistenti per l'integrazione con altri servizi di Azure, se possibile. In alcuni casi, può essere necessario un componente o una soluzione specifica del servizio.

    * __Azure Data Lake Store__: le topologie basate su Java possono accedere a Data Lake Store usando il bolt Storm-HDFS con lo schema URI `adl://`. Per un esempio dell'uso del bolt Storm-HDFS, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store).

    * __Archiviazione BLOB di Azure__ (se usato come risorsa di archiviazione per HDInsight): le topologie basate su Java possono accedere all'archivio BLOB di Azure associato al cluster usando il bolt Storm-HDFS con lo schema URI `wasb://`.

    * __Hub eventi di Azure__: è accessibile con i componenti Microsoft EventHubSpout ed EventHubBolt. Questi componenti sono scritti in Java e forniti come file JAR autonomo.

    Per altre informazioni sullo sviluppo di soluzioni Java, vedere [Sviluppare una topologia basata su Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

  * Per lo sviluppo __C#__, in genere è possibile usare .NET SDK per il servizio di Azure. In alcuni casi l'SDK può essere basato su framework non disponibili in Linux (il sistema operativo host per HDInsight 3.4 e versioni successive). In questo caso, è possibile usare i componenti Java dalla soluzione C#.

    * Gli esempi dell'uso di __SQL DB__, __DocumentDB__, __EventHub__ e __HBase__ sono inclusi come modelli in Azure Data Lake Tools per Visual Studio. Vedere [Sviluppare una topologia C# per Storm in HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) per altre informazioni.

    * __Hub eventi di Azure__: per un esempio dell'uso di componenti Java da una soluzione C#, vedere [Elaborare eventi di Hub eventi di Azure con Storm in HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md).

    Per altre informazioni sullo sviluppo di soluzioni C#, vedere [Sviluppare una topologia C# per Storm in HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="reliability"></a>Affidabilità

Apache Storm garantisce che ogni messaggio in arrivo verrà completamente elaborato, anche se l'analisi dei dati è distribuita tra centinaia di nodi.

Il **nodo Nimbus** offre funzionalità simili a quelle di JobTracker di Hadoop e assegna attività agli altri nodi del cluster tramite **Zookeeper**. I nodi Zookeeper assicurano la coordinazione del cluster e facilitano la comunicazione tra Nimbus e il processo **Supervisor** sui nodi di lavoro. Se un nodo di elaborazione si arresta, il nodo Nimbus riceve una notifica e provvede ad assegnare l'attività e i dati associati a un altro nodo.

La configurazione predefinita di Apache Storm prevede un solo nodo Nimbus. Storm in HDInsight esegue invece due nodi Nimbus. In caso di errore del nodo primario, il cluster HDInsight passerà a quello secondario. Nel frattempo, il nodo primario verrà ripristinato.

![Diagramma di Nimbus, Zookeeper e Supervisor](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>Scalabilità

Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di modificare il numero di nodi, anche durante l'elaborazione dei dati.

> [!NOTE]
> Per sfruttare i vantaggi di nuovi nodi aggiunti tramite la scalabilità, è necessario ribilanciare le topologie avviate prima dell'aumento delle dimensioni del cluster.

### <a name="support"></a>Supporto

Storm in HDInsight viene fornito con supporto completo di livello enterprise 24 ore su 24, 7 giorni su 7. Storm in HDInsight dispone inoltre di un contratto di servizio con disponibilità del 99,9%. Questo garantisce che il cluster avrà connettività esterna per almeno il 99,9% del tempo.

## <a name="common-use-cases-for-real-time-analytics"></a>Casi comuni di uso dell’analisi in tempo reale

Di seguito sono descritti alcuni scenari comuni in cui è possibile usare Apache Storm in HDInsight. Per informazioni sugli scenari reali, leggere l'articolo che descrive [in che modo le società stanno usando Storm](https://storm.apache.org/documentation/Powered-By.html).

* Internet delle cose
* Rilevamento delle frodi
* Analisi di social media
* ETL (Extract Transform Load)
* Monitoraggio della rete
* Search
* Mobile Engagement

## <a name="how-is-data-in-hdinsight-storm-processed"></a>Come vengono elaborati i dati in HDInsight Storm?

Apache Storm elabora **topologie** al posto dei processi MapReduce con cui l'utente ha familiarità in HDInsight o Hadoop. Un cluster Storm in HDInsight contiene due tipi di nodi, i nodi head che eseguono **Nimbus** e i nodi di lavoro che eseguono **Supervisor**.

* **Nimbus**: simile a JobTracker di Hadoop, è responsabile della distribuzione del codice nell'intero cluster, dell'assegnazione delle attività alle macchine virtuali e del monitoraggio degli errori. HDInsight fornisce due nodi Nimbus, quindi non esiste un singolo punto di errore per un cluster Storm in HDInsight.
* **Supervisor**: il supervisore di ciascun nodo di lavoro, responsabile dell'avvio e dell'arresto dei **processi di lavoro** nel nodo.
* **Processo di lavoro**: un processo di lavoro esegue un subset di una **topologia**. Una topologia in esecuzione viene distribuita tra più processi di lavoro nel cluster.
* **Topologia**: definisce un grafico di calcolo che elabora i **flussi** di dati. A differenza dei processi MapReduce, le topologie restano in esecuzione finché non vengono arrestate.
* **Flusso**: una raccolta non associata di **tuple**. I flussi vengono prodotti dagli **spout** e dai **bolt** e vengono utilizzati dai **bolt**.
* **Tupla**: un elenco denominato di valori tipizzati dinamicamente.
* **Spout**: utilizza i dati dall'origine dati e genera uno o più **flussi**.
  
  > [!NOTE]
  > In molti casi i dati vengono letti da una coda come Kafka o Hub eventi di Azure. La coda assicura la persistenza dei dati in caso di interruzione.

* **Bolt**: utilizza i **flussi**, esegue l'elaborazione sulle **tuple** e può generare **flussi**. I bolt sono responsabili anche della scrittura dei dati in una risorsa di archiviazione esterna, ad esempio una coda, HDInsight, HBase, un BLOB o un altro archivio dati.
* **Apache Thrift**: un framework software per lo sviluppo di servizi scalabili per più linguaggi. Consente di creare servizi che funzionano con C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk e altri linguaggi.
  
  * **Nimbus** è un servizio Thrift e una **topologia** è una definizione Thrift. È quindi possibile sviluppare le topologie usando diversi linguaggi di programmazione.

Per altre informazioni sui componenti Storm, vedere l'[esercitazione su Storm][apachetutorial] nel sito Web apache.org.

## <a name="what-programming-languages-can-i-use"></a>Quali linguaggi di programmazione si possono usare?

Il cluster Storm in HDInsight offre supporto per C#, Java e Python.

### <a name="c35"></a>C&#35;

Data Lake Tools per Visual Studio consente agli sviluppatori .NET di progettare e implementare una topologia in C#. È inoltre possibile creare topologie ibride che usano componenti Java e C#.

Per altre informazioni, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="java"></a>Java

Molti esempi Java illustrati sono di tipo Java normale o Trident. Trident è un'astrazione di alto livello che semplifica diverse operazioni, ad esempio join, aggregazioni, raggruppamento e filtro. Trident, tuttavia, si applica a batch di tuple, mentre una soluzione Java non elaborata consente di elaborare un flusso una tupla alla volta.

Per altre informazioni su Trident, vedere l' [esercitazione su Trident](https://storm.apache.org/documentation/Trident-tutorial.html) nel sito Web apache.org.

Per esempi di topologie Java e Trident, vedere l’ [elenco di esempi di topologie Storm](hdinsight-storm-example-topology.md) o gli esempi storm-starter nel cluster HDInsight.

Gli esempi di storm-starter sono disponibili nella directory **/usr/hdp/current/storm-client/contrib/storm-starter** nel cluster HDInsight.

## <a name="what-are-some-common-development-patterns"></a>Quali sono alcuni dei modelli di sviluppo più comuni?

### <a name="guaranteed-message-processing"></a>Elaborazione garantita dei messaggi

Storm può fornire diversi livelli di elaborazione garantita dei messaggi. Un'applicazione Storm di base, ad esempio, può garantire un'elaborazione at-least-once, mentre Trident può garantire un'elaborazione exactly-once.

Per altre informazioni, vedere la sezione sulle [garanzie relative all'elaborazione dati](https://storm.apache.org/about/guarantees-data-processing.html) nel sito Web apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Il modello che prevede la lettura di una tupla di input, la generazione di nessuna o più tuple e l'inserimento della tupla di input subito dopo la fine del metodo di esecuzione è così frequente che Storm fornisce l'interfaccia [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) per automatizzarlo.

### <a name="joins"></a>Join

La creazione di un join di due flussi di dati varia a seconda delle applicazioni. È possibile, ad esempio, creare un join di ogni tupla da più flussi in un unico nuovo flusso oppure è possibile creare un solo join di più batch di tuple per una specifica finestra. In entrambi i casi il join può essere eseguito usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), che rappresenta un metodo per definire in che modo le tuple vengono indirizzate ai bolt.

Nell'esempio Java seguente, fieldsGrouping viene usato per indirizzare le tuple originate dai componenti "1", "2" e "3" al bolt **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Creazione di batch

La creazione di batch può essere eseguita in diversi modi. Con una topologia C# o Java è possibile usare un contatore semplice per creare un batch con un numero X di tuple prima di emetterle oppure usare un meccanismo a tempo interno noto come "tupla tick" per generare un batch ogni X secondi.

Per un esempio dell'uso di una tupla tick da un componente C#, vedere [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Trident invece si basa sull'elaborazione di batch di tuple.

### <a name="caching"></a>Memorizzazione nella cache

La memorizzazione nella cache viene spesso usata come meccanismo per velocizzare l'elaborazione perché conserva in memoria le risorse usate di frequente. Poiché una topologia viene distribuita in più nodi, e in più processi all'interno di ogni nodo, è consigliabile usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) per garantire che le tuple contenenti i campi usati per la ricerca nella cache vengano sempre indirizzate allo stesso processo. Questo evita la duplicazione delle voci della cache nei processi.

### <a name="streaming-top-n"></a>Streaming dei primi N elementi

Quando la topologia dipende dal calcolo di un valore "primi N", ad esempio le prime cinque tendenze su Twitter, è necessario calcolare il valore "primi N" in parallelo e quindi unire l'output dei calcoli in un valore globale. A questo scopo, usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) per l'indirizzamento per campo al bolt parallelo (che esegue il partizionamento dei dati in base al valore del campo) e infine eseguire l'indirizzamento a un bolt che determina globalmente il valore "primi N".

Per un esempio, vedere [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) .

## <a name="what-type-of-logging-does-storm-use"></a>Che tipo di registrazione usa Storm?

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



<!--HONumber=Feb17_HO3-->


