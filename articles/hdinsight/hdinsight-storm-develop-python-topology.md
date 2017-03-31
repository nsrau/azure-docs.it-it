---
title: Usare componenti Python in una topologia Storm in HDinsight | Documentazione Microsoft
description: "Informazioni su come usare i componenti Python con Apache Storm in Azure HDInsight. Si apprenderà come usare i componenti Python da una topologia Storm basata su Java e su Clojure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8b32aa77e1dbe18076d73e10914b59be107c3588
ms.lasthandoff: 03/25/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Sviluppare topologie Apache Storm con Python in HDInsight

Apache Storm supporta più linguaggi, consentendo di combinare in un'unica topologia componenti in più lingue. In questo documento si apprenderà come usare i componenti Python nelle topologie Storm basate su Clojure e su Java in HDInsight.

> [!IMPORTANT]
> In questo documento viene descritta la procedura per l'uso sia di cluster HDInsight basati su Linux sia di cluster HDInsight basati su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

* Python 2.7 o versione successiva
* Java JDK 1.7 o versione successiva
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Supporto per più linguaggi in Storm

Storm è stato progettato per funzionare con componenti scritti in qualsiasi linguaggio di programmazione, ma ciò richiede che i componenti riconoscano come interagire con la [definizione di Thrift per Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Per Python viene fornito un modulo, come parte del progetto Apache Storm, che consente di interfacciarsi facilmente con Storm. Questo modulo è disponibile all'indirizzo [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Poiché Apache Storm è un processo Java eseguito in Java Virtual Machine (JVM), i componenti scritti in altri linguaggi vengono eseguiti come sottoprocessi. I bit di Storm eseguiti nella JVM comunicano con questi sottoprocessi usando messaggi JSON inviati tramite stdin/stdout. Altri dettagli sulla comunicazione tra i componenti sono disponibili nella documentazione relativa al [protocollo Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

### <a name="the-storm-module"></a>Modulo Storm
Il modulo storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) fornisce i bit necessari per creare componenti Python in grado di funzionare con Storm.

Fornisce elementi come `storm.emit` per l'emissione di tuple e `storm.logInfo` per la scrittura nei log. È consigliabile leggere l'intero contenuto di questo file per comprendere quali elementi fornisce.

## <a name="challenges"></a>Problematiche
Con il modulo **storm.py** è possibile creare spout Python che usano i dati e bolt che li elaborano. Tuttavia, la definizione complessiva della topologia Storm che fornisce il collegamento per la comunicazione tra i componenti è comunque scritta con Java o Clojure. Se si usa Java, è necessario creare anche i componenti Java che agiscono come interfaccia per i componenti Python.

Poiché inoltre i cluster Storm vengono eseguiti in modalità distribuita, è necessario assicurarsi che tutti i moduli richiesti dai componenti Python siano disponibili in tutti i nodi di lavoro del cluster. Storm non offre un modo semplice per eseguire questa operazione per le risorse in più linguaggi, quindi è necessario includere tutte le dipendenze come parte del file JAR per la topologia oppure installare manualmente le dipendenze in ogni nodo di lavoro del cluster.

### <a name="java-vs-clojure-topology-definition"></a>Definizione della topologia Java rispetto a Clojure
Dei due metodi per la definizione di una topologia, Clojure è di gran lunga il più facile e chiaro, perché consente di fare riferimento direttamente ai componenti Python nella definizione della topologia. Per le definizioni della topologia basata su Java, è necessario specificare anche i componenti Java che gestiscono elementi come la dichiarazione dei campi nelle tuple restituite dai componenti Python.

Entrambi i metodi sono descritti in questo documento insieme ai progetti di esempio.

## <a name="python-components-with-a-java-topology"></a>Componenti Python con una topologia Java
> [!NOTE]
> Questo esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory **JavaTopology** . Si tratta di un progetto basato su Maven. Se non si ha familiarità con Maven, vedere [Sviluppare topologie basate su Java con Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md) per altre informazioni sulla creazione di un progetto Maven per una topologia Storm.
> 
> 

Una topologia basata su Java che usa Python, o altri componenti del linguaggio JVM, inizialmente sembra che usi i componenti Java, ma osservando ognuno degli spout o dei bolt Java, si noterà codice simile al seguente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

È a questo punto che Java richiama Python ed esegue lo script che contiene la logica dei bolt effettiva. Gli spout o i bolt Java, in questo esempio, dichiarano semplicemente i campi nella tupla che sarà generata dal componente Python sottostante.

In questo esempio i file Python effettivi sono archiviati nella directory `/multilang/resources` . Alla directory `/multilang` viene fatto riferimento nel file **pom.xml**:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

Include tutti i file presenti nella cartella `/multilang` nel file JAR che sarà compilato da questo progetto.

> [!IMPORTANT]
> Tenere presente che viene specificata solo la directory `/multilang`, non `/multilang/resources`. Storm prevede che le risorse non JVM siano inserite in una directory `resources` , quindi ne viene già effettuata la ricerca internamente. L'inserimento di componenti in questa cartella consente di farvi riferimento per nome nel codice Java. Ad esempio, `super("python", "countbolt.py");`. Da un altro punto di vista, Storm considera la directory `resources` come radice (/) quando accede alle risorse in più linguaggi.
> 
> Per questo progetto di esempio il modulo `storm.py` è incluso nella directory `/multilang/resources`.
> 
> 

### <a name="build-and-run-the-project"></a>Compilare ed eseguire il progetto
Per eseguire il progetto localmente, usare semplicemente il comando Maven seguente per compilarlo ed eseguirlo in modalità locale:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Per terminare il processo, usare CTRL+C.

Per distribuire il progetto in un cluster HDInsight che esegue Apache Storm, seguire questa procedura:

1. Compilare un file uberjar:
   
        mvn package
   
    Verrà creato un file denominato **WordCount--1.0-SNAPSHOT.jar** nella directory `/target` di questo progetto.
2. Caricare il file JAR nel cluster Hadoop usando uno dei metodi seguenti:
   
   * Per i cluster HDInsight **basati su Linux:**usare `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` per copiare il file JAR nel cluster, sostituendo USERNAME con il nome utente SSH e CLUSTERNAME con il nome del cluster HDInsight.
     
       Una volta che il file è stato caricato, connettersi al cluster tramite SSH e avviare la topologia con `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * Per i cluster HDInsight **basati su Windows:**connettersi al Dashboard di Storm passando a HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire CLUSTERNAME con il nome del cluster HDInsight e fornire il nome e la password dell'amministratore, quando richiesto.
     
       Usando il modulo, eseguire le azioni seguenti:
     
     * **Jar File**: selezionare **Browse**, quindi il file **wordcount-1.0-SNAPSHOT.jar**
     * **Class Name**: immettere `com.microsoft.example.WordCount`
     * **Additional Paramters**: immettere un nome descrittivo, ad esempio `wordcount`, per identificare la topologia
       
       Infine selezionare **Submit** per avviare la topologia.

> [!NOTE]
> Una volta avviata una topologia Storm, questa rimane in esecuzione finché non viene arrestata (terminata). Per arrestare la topologia, usare il comando `storm kill TOPOLOGYNAME` dalla riga di comando, ad esempio una sessione SSH con un cluster Linux, oppure usare l'interfaccia utente di Storm, selezionare la topologia e quindi fare clic sul pulsante **Kill**.
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>Componenti Python con una topologia Clojure
> [!NOTE]
> Questo esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory **ClojureTopology** .
> 
> 

Questa topologia è stata creata usando [Leiningen](http://leiningen.org) per [creare un nuovo progetto Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Successivamente, sono state apportate le modifiche seguenti al progetto con scaffolding:

* **project.clj**: sono state aggiunte dipendenze per Storm ed esclusioni per gli elementi che potrebbero causare un problema durante la distribuzione nel server HDInsight.
* **resources/resources**: Leiningen crea una directory `resources` predefinita, tuttavia i file aggiunti qui appaiono come aggiunti alla radice del file JAR creato da questo progetto, mentre Storm prevede la presenza dei file in una sottodirectory denominata `resources`. È stata quindi aggiunta una sottodirectory e i file Python vengono archiviati in `resources/resources`. In fase di esecuzione sarà considerata come la radice (/) per l'accesso ai componenti di Python.
* **src/wordcount/core.clj**: questo file, a cui fa riferimento il file **project.clj**, contiene la definizione della topologia. Per altre informazioni sull'uso di Clojure per definire una topologia Storm, vedere [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

### <a name="build-and-run-the-project"></a>Compilare ed eseguire il progetto
**Per compilare ed eseguire il progetto localmente**, usare il comando seguente:

    lein clean, run

Per arrestare la topologia, usare **CTRL+C**.

**Per compilare un file uberjar e distribuirlo in HDInsight**, seguire questa procedura:

1. Creare un file uberjar contenente la topologia e le dipendenze necessarie:
   
        lein uberjar
   
    Verrà creato un nuovo file denominato `wordcount-1.0-SNAPSHOT.jar` nella directory `target\uberjar+uberjar` .
2. Usare uno dei metodi seguenti per distribuire ed eseguire la topologia in un cluster HDInsight:
   
   * **HDInsight basato su Linux**
     
     1. Copiare il file nel nodo head del cluster HDInsight tramite `scp`. ad esempio:
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         Sostituire USERNAME con un utente SSH per il cluster e CLUSTERNAME con il nome del cluster HDInsight.
     2. Dopo aver copiato il file nel cluster, usare SSH per connettersi al cluster e inviare il processo. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     
     3. Dopo la connessione, usare il comando seguente per avviare la topologia:
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **HDInsight basato su Windows**
     
     1. Connettersi al Dashboard di Storm passando a HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire CLUSTERNAME con il nome del cluster HDInsight e fornire il nome e la password dell'amministratore, quando richiesto.
     2. Usando il modulo, eseguire le azioni seguenti:
        
        * **Jar File**: selezionare **Browse**, quindi il file **wordcount-1.0-SNAPSHOT.jar**
        * **Class Name**: immettere `wordcount.core`
        * **Additional Paramters**: immettere un nome descrittivo, ad esempio `wordcount`, per identificare la topologia
          
          Infine selezionare **Submit** per avviare la topologia.

> [!NOTE]
> Una volta avviata una topologia Storm, questa rimane in esecuzione finché non viene arrestata (terminata). Per arrestare la topologia, usare il comando `storm kill TOPOLOGYNAME` dalla riga di comando, in una sessione SSH con un cluster Linux, oppure usare l'interfaccia utente di Storm, selezionare la topologia e quindi fare clic sul pulsante **Kill**.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare i componenti Python da una topologia Storm. Vedere i documenti seguenti per altri modi di usare Python con HDInsight:

* [Come usare Python per il flusso di processi MapReduce](hdinsight-hadoop-streaming-python.md)
* [Come usare funzioni definite dall'utente Python in Pig e Hive](hdinsight-python.md)


