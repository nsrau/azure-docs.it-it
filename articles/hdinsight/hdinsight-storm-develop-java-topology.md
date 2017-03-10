---
title: Sviluppare topologie basate su Java per Apache Storm | Documentazione Microsoft
description: Informazioni su come creare topologie Storm in Java mediante la creazione di una semplice topologia di conteggio parole.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 3b9dfffe17272296ef10a78b3cf25570109679c7
ms.lasthandoff: 03/01/2017

---
# <a name="use-maven-to-develop-a-java-based-word-count-topology-for-storm-on-hdinsight"></a>Usare Maven per sviluppare una topologia di conteggio parole basata su Java per Storm in HDInsight

Informazioni su come creare una topologia basata su Java per Apache Storm in HDInsight usando Maven. Questa procedura consente di creare un'applicazione di conteggio parole di base mediante Maven e Java, dove la topologia è definita in Java. Quindi, si apprenderà come definire la topologia usando il framework Flux.

> [!NOTE]
> Il framework Flux è disponibile in Storm 0.10.0 o versioni successive. Storm 0.10.0 è disponibile con HDInsight 3.3 e 3.4.


Dopo aver completato i passaggi descritti in questo documento, è possibile distribuire la topologia ad Apache Storm in HDInsight.

> [!NOTE]
> Una versione completa delle topologie create in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Prerequisiti

* [Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)

* [Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven è un sistema per la compilazione di progetti per Java.

* Un editor di testo o ambiente IDE.

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Quando si installa Java e JDK, è possibile impostare le variabili di ambiente indicate di seguito. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`

* **PATH** : deve contenere i percorsi seguenti:

  * **JAVA_HOME** o il percorso equivalente

  * **JAVA_HOME\bin** o il percorso equivalente

  * Directory in cui è installato Maven

## <a name="create-a-maven-project"></a>Creare un progetto Maven

Dalla riga di comando usare il comando seguente per creare un progetto Maven denominato **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

> [!NOTE]
> Se si utilizza PowerShell, è necessario racchiudere i parametri `-D` tra virgolette.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

Questo comando crea una nuova directory denominata `WordCount` nella posizione corrente, contenente un progetto Maven di base.

La directory `WordCount` contiene gli elementi seguenti:

* `pom.xml`: contiene le impostazioni per il progetto Maven.
* `src\main\java\com\microsoft\example`: contiene il codice dell'applicazione.
* `src\test\java\com\microsoft\example`: contiene i test per l'applicazione. 

### <a name="remove-the-example-code"></a>Rimuovere il codice di esempio

Eliminare i file dell'applicazione e il test generato:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-properties"></a>Aggiungere le proprietà

Maven consente di definire i valori a livello di progetto denominati proprietà. Aggiungere il testo seguente dopo la riga `<url>http://maven.apache.org</url>`:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    Storm 0.10.0 is for HDInsight 3.3 and 3.4.
    To find the version information for earlier HDInsight cluster
    versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
    -->
    <storm.version>0.10.0</storm.version>
</properties>
```

È ora possibile usare questi valori in altre sezioni di `pom.xml`. Ad esempio, quando si specifica la versione dei componenti Storm, è possibile usare `${storm.version}` anziché codificare un valore.

## <a name="add-dependencies"></a>Aggiungere le dipendenze

È necessario aggiungere una dipendenza per i componenti Storm. Aprire il file `pom.xml` e aggiungere il codice seguente nella sezione `<dependencies>`:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

In fase di compilazione, Maven usa queste informazioni per cercare **storm-core** nel repository. Viene innanzitutto esaminato il repository del computer locale. Se i file non sono presenti, verranno scaricati tramite Maven dal repository pubblico Maven e archiviati nel repository locale.

> [!NOTE]
> Si noti la riga `<scope>provided</scope>` in questa sezione. Questa impostazione indica a Maven di escludere **storm-core** da qualsiasi file con estensione JAR creato, poiché viene fornito dal sistema.

## <a name="build-configuration"></a>Configurare la compilazione

I plug-in Maven consentono di personalizzare le fasi di compilazione di un progetto, ad esempio il modo in cui tale progetto viene compilato o inserito come pacchetto in un file con estensione jar. Aprire il file `pom.xml` e aggiungere il codice seguente direttamente sopra la riga `</project>`.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Questa sezione viene usata per aggiungere plug-in, risorse e altre opzioni di configurazione della compilazione. Per un riferimento completo del file **pom.xml**, vedere [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Aggiungere plug-in

Per le topologie Storm, il [plug-in Exec Maven](http://mojo.codehaus.org/exec-maven-plugin/) risulta particolarmente utile in quanto consente di eseguire facilmente la topologia nell'ambiente di sviluppo in uso. Aggiungere quanto segue alla sezione `<plugins>` del file `pom.xml` per includere il plug-in Exec Maven:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.4.0</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

Anche il [plug-in Apache Maven Compiler](http://maven.apache.org/plugins/maven-compiler-plugin/), usato per modificare le opzioni di compilazione, è molto utile. Questo consente di modificare la versione di Java usata da Maven come versione di origine e destinazione dell'applicazione.

* Per HDInsight __3.4 o versioni precedenti__, impostare la versione di origine e di destinazione di Java su __1.7__.

* Per HDInsight __3.5__, impostare la versione di origine e di destinazione di Java su __1.8__.

Aggiungere il testo seguente nella sezione `<plugins>` del file `pom.xml` per includere il plug-in Apache Maven Compiler. Questo esempio specifica la versione 1.8, quindi la versione di HDInsight di destinazione è 3.5.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

### <a name="configure-resources"></a>Configure resources

La sezione delle risorse consente di includere le risorse non di codice, ad esempio i file di configurazione richiesti dai componenti della topologia. In questo esempio aggiungere il testo seguente nella sezione `<resources>` del file pom.xml.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

In questo esempio viene aggiunta la directory delle risorse nella radice del progetto (`${basedir}`) come posizione contenente le risorse e include il file denominato `log4j2.xml`. Questo file viene usato per configurare le informazioni registrate dalla topologia.

## <a name="create-the-topology"></a>Creare la topologia

Una topologia Storm basata su Java è costituita da tre componenti che è necessario creare o a cui è necessario fare riferimento come dipendenza.

* **Spout**: legge i dati da origini esterne e genera flussi di dati nella topologia.

* **Bolt**: esegue l'elaborazione sui flussi generati dagli spout o da altri bolt e genera uno o più flussi.

* **Topologia**: definisce il modo in cui vengono disposti gli spout e i bolt e fornisce il punto di ingresso per la topologia.

### <a name="create-the-spout"></a>Creare lo spout

Per ridurre i requisiti relativi all'impostazione di origini dati esterne, lo spout seguente genera semplicemente frasi casuali. Si tratta di una versione modificata di uno spout fornito con gli esempi di [Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Per uno spout in grado di leggere da un'origine dati esterna, vedere uno degli esempi seguenti:
> 
> * [TwitterSampleSpout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): spout di esempio che legge da Twitter
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): spout che legge da Kafka

Per lo spout, creare un file denominato `RandomSentenceSpout.java` nella directory `src\main\java\com\microsoft\example` e usare il testo seguente come contenuto:

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

Si consiglia di leggere con attenzione i commenti del codice per comprendere il funzionamento dello spout.

> [!NOTE]
> Anche se questa topologia usa soltanto uno spout, altre topologie possono avere diversi spout che inseriscono dati da diverse origini.

### <a name="create-the-bolts"></a>Creare i bolt

I bolt gestiscono l'elaborazione dei dati. Questa topologia usa due bolt:

* **SplitSentence**: divide le frasi generate da **RandomSentenceSpout** in singole parole.

* **WordCount**: conta le occorrenze di ciascuna parola.

> [!NOTE]
> I bolt eseguono qualsiasi tipo di attività, ad esempio calcolo, persistenza o comunicazione con componenti esterni.

Nella directory `src\main\java\com\microsoft\example` creare due nuovi file, `SplitSentence.java` e `WordCount.Java`. Usare come contenuto dei file il testo riportato di seguito:

**SplitSentence**

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

**WordCount**

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

Si consiglia di leggere con attenzione i commenti del codice per comprendere il funzionamento di ogni bolt.

### <a name="define-the-topology"></a>Definire la topologia

La topologia collega gli spout e i bolt in un grafico, che definisce il flusso di dati tra i componenti. Fornisce inoltre suggerimenti di parallelismo usati da Storm durante la creazione di istanze di componenti all'interno del cluster.

La seguente immagine è un diagramma di base del grafico dei componenti della topologia.

![diagramma che mostra la disposizione degli spout e dei bolt](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Per implementare la topologia, creare un file denominato `WordCountTopology.java` nella directory `src\main\java\com\microsoft\example`. Usare come contenuto del file il testo riportato di seguito:

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

Si consiglia di leggere con attenzione i commenti del codice per comprendere in che modo la topologia viene definita e quindi inviata al cluster.

### <a name="configure-logging"></a>Configurare la registrazione

Storm usa Apache Log4j per registrare le informazioni. Se non si configura la registrazione, la topologia genera informazioni di diagnostica. Per controllare ciò che viene registrato, creare un file denominato `log4j2.xml` nella directory `resources`. Usare il testo seguente come contenuto del file.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Ciò consente di configurare un nuovo logger per la classe `com.microsoft.example`, che include i componenti in questa topologia di esempio. Il livello è impostato sul monitoraggio per questo logger tramite il quale acquisisce le informazioni di registrazione generate dai componenti in questa topologia.

La sezione `<Root level="error">` configura il livello radice di registrazione, ovvero tutti gli elementi non presenti in `com.microsoft.example`, per poter registrare solo le informazioni relative agli errori.

Per altre informazioni sulla configurazione della registrazione per Log4j, vedere [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> Storm versione 0.10.0 e successive usano Log4j 2.x. Le versioni precedenti usano Log4j 1.x, che impiega un formato diverso per la configurazione del log. Per informazioni sulla configurazione precedente, vedere [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testare la topologia in locale

Dopo aver salvato i file, usare il comando seguente per testare la topologia in locale.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Durante l'esecuzione, la topologia mostra le informazioni di avvio. Il testo seguente è un esempio di output del conteggio parole:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Guardando la registrazione generata dal bolt WordCount, si nota che la parola "and" è stata generata 113 volte. Il conteggio continua ad aumentare fintanto che la topologia è in esecuzione perché lo spout emette continuamente le stesse frasi.

C'è un intervallo di 5 secondi tra l'emissione di parole e i conteggi. Il componente **WordCount** è configurato per emettere informazioni soltanto quando arriva un segno di tupla e richiede che tali tuple vengano recapitate solo ogni cinque secondi per impostazione predefinita.

## <a name="convert-the-topology-to-flux"></a>Convertire la topologia in Flux

Flux è un nuovo framework disponibile con Storm 0.10.0 e versioni successive, che consente di separare la configurazione dall'implementazione. I componenti (bolt e spout) sono ancora definiti in Java, ma la topologia viene definita mediante un file YAML.

Il file YAML definisce i componenti da utilizzare per la topologia, il flusso dei dati tra i componenti e quali valori usare durante l'inizializzazione dei componenti. È possibile includere un file YAML come parte del file con estensione JAR oppure usare un file esterno YAML.

1. Spostare il file `WordCountTopology.java` fuori dal progetto. In precedenza questo file ha definito la topologia, ma non è necessario con Flux.

2. Creare un nuovo file denominato `topology.yaml` nella directory `resources`. Usare il testo seguente come contenuto del file.
    
    ```yaml
    # topology definition

    # name to be used when submitting. This is what shows up...
    # in the Storm UI/storm command line tool as the topology name
    # when submitted to Storm
    name: "wordcount"

    # Topology configuration
    config:
    # Hint for the number of workers to create
    topology.workers: 1

    # Spout definitions
    spouts:
    - id: "sentence-spout"
        className: "com.microsoft.example.RandomSentenceSpout"
        # parallelism hint
        parallelism: 1

    # Bolt definitions
    bolts:
    - id: "splitter-bolt"
        className: "com.microsoft.example.SplitSentence"
        parallelism: 1

    - id: "counter-bolt"
        className: "com.microsoft.example.WordCount"
        constructorArgs:
        - 10
        parallelism: 1

    # Stream definitions
    streams:
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
        # The stream emitter
        from: "sentence-spout"
        # The stream consumer
        to: "splitter-bolt"
        # Grouping type
        grouping:
        type: SHUFFLE

    - name: "Splitter -> Counter"
        from: "splitter-bolt"
        to: "counter-bolt"
        grouping:
        type: FIELDS
        # field(s) to group on
        args: ["word"]
    ```

    Dedicare alcuni minuti alla lettura e comprensione della funzione di ciascuna sezione e di come questa sia correlata alla definizione basata su Java nel file **WordCountTopology.java**.

3. Apportare le modifiche seguenti al file `pom.xml`.
   
   * Aggiungere la nuova dipendenza seguente nella sezione `<dependencies>` :
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Aggiungere il plug-in seguente per la sezione `<plugins>` . Questo plug-in gestisce la creazione di un pacchetto (file jar) per il progetto e applica alcune trasformazioni specifiche a Flux durante la creazione del pacchetto.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * Nella sezione `<configuration>` di **exec-maven-plugin** sostituire il valore di `<mainClass>` con `org.apache.storm.flux.Flux`. Questa impostazione consente a Flux di gestire l'esecuzione in locale della topologia nell'ambiente di sviluppo.

   * Nella sezione `<resources>`, aggiungere quanto segue a `<includes>`. Ciò include il file YAML che definisce la topologia come parte del progetto.
     
        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testare la topologia Flux in locale

1. Usare la seguente procedura per compilare ed eseguire la topologia Flux usando Maven:
   
        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
   
    Se si utilizza PowerShell, usare il comando seguente:
   
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
   
    Se invece si utilizza un sistema Linux/Unix/OS X e [Storm è stato installato nell'ambiente di sviluppo](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), è possibile utilizzare i comandi seguenti:
   
        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
   
    Il parametro `--local` esegue la topologia in modalità locale nell'ambiente di sviluppo. Il parametro `-R /topology.yaml` usa la risorsa file `topology.yaml` dal file jar per definire la topologia.
   
    Durante l'esecuzione, la topologia mostra le informazioni di avvio. Il testo seguente è un esempio di output:
   
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
   
    C'è un ritardo di 10 secondi tra i batch delle informazioni registrate.

2. Creare una copia del file `topology.yaml` dal progetto. Assegnare al nuovo file il nome `newtopology.yaml`. Nel file `newtopology.yaml` individuare la sezione seguente e modificare il valore di `10` su `5`. Ciò consente di modificare l'intervallo tra i batch di emissione del numero di parole da 10 secondi a 5.
   
        - id: "counter-bolt"
        className: "com.microsoft.example.WordCount"
        constructorArgs:
        - 5
        parallelism: 1

3. Per eseguire la topologia, usare il comando seguente:
   
        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
   
    In alternativa, se si dispone di Storm nell'ambiente di sviluppo Linux/Unix/OS X:
   
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
   
    Modificare `/path/to/newtopology.yaml` sul percorso al file newtopology.yaml creato nel passaggio precedente. Questo comando usa il file newtopology.yaml come definizione della topologia. Siccome il parametro `compile` non è stato incluso, Maven usa la versione del progetto creato nei passaggi precedenti.
   
    Una volta avviata la topologia, si dovrebbe notare che il tempo tra i batch emessi è cambiato per riflettere il valore in newtopology.yaml. Pertanto, è possibile modificare la configurazione tramite un file YAML senza dover ricompilare la topologia.

Per ulteriori informazioni su queste e altre funzionalità del framework Flux, vedere [Flux (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

## <a name="trident"></a>Trident

Trident è un'astrazione di alto livello fornita da Storm che supporta l'elaborazione con informazioni sullo stato. Il principale vantaggio offerto da Trident è la garanzia che ogni messaggio introdotto nella topologia viene elaborato una sola volta. Questo risultato è difficile da ottenere in una topologia Java, che garantisce che i messaggi vengano elaborati almeno una volta. Esistono altre differenze, ad esempio la disponibilità di componenti predefiniti che possono essere usati senza che sia necessario creare bolt. I bolt sono infatti sostituiti da componenti meno generici, ad esempio filtri, proiezioni e funzioni.

È possibile creare applicazioni Trident mediante progetti Maven L'unica differenza consiste nel codice. Trident, inoltre, non è (attualmente) utilizzabile con il framework Flux.

Per altre informazioni su Trident, vedere la [panoramica dell'API Trident](http://storm.apache.org/documentation/Trident-API-Overview.html).

Per un'applicazione Trident di esempio, vedere [Temi di tendenza Twitter con Apache Storm in HDInsight](hdinsight-storm-twitter-trending.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come creare una topologia Storm con Java, è possibile passare agli argomenti seguenti:

* [Distribuzione e gestione di topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Per altri esempi di topologie Storm, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).


