---
title: Esempio Java di topologia Apache Storm - Azure HDInsight
description: Informazioni su come creare topologie apache Storm in Java mediante la creazione di una topologia di conteggio parole di esempio.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm, esempio di apache storm, storm java, esempio di topologia storm
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2c1c144899189e2320d1388fca848fa3d7ec2257
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122081"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Creare una topologia Apache Storm in Java

Informazioni su come creare una topologia basata su Java per [Apache Storm](https://storm.apache.org/). In questo caso, si crea una topologia Storm che implementa un'applicazione di conteggio delle parole. Per compilare il progetto e creare il pacchetto si usa [Apache Maven](https://maven.apache.org/). Quindi, imparare a definire la topologia usando il [Apache Storm Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) framework.

Dopo aver completato i passaggi descritti in questo documento, è possibile distribuire la topologia ad Apache Storm in HDInsight.

> [!NOTE]  
> La versione completa degli esempi di topologia Storm creati in questo documento è disponibile presso il sito [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Prerequisiti

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base a Apache.  Maven è un sistema di compilazione per progetti Java.

## <a name="test-environment"></a>Ambiente di test
L'ambiente usato per questo articolo è stato un computer che eseguono Windows 10.  I comandi sono stati eseguiti in un prompt dei comandi e i vari file sono stati modificati con blocco note.

Da un prompt dei comandi, immettere i comandi seguenti per creare un ambiente di lavoro:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Creare un progetto Maven

Immettere il comando seguente per creare un progetto Maven denominato **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Questo comando crea una nuova directory denominata `WordCount` nella posizione corrente, contenente un progetto Maven di base. Il secondo comando modifica la directory di lavoro presenti `WordCount`. Il terzo comando crea una nuova directory, `resources`, che verrà usato in un secondo momento.  La directory `WordCount` contiene gli elementi seguenti:

* `pom.xml`: contiene le impostazioni per il progetto Maven.
* `src\main\java\com\microsoft\example`: contiene il codice dell'applicazione.
* `src\test\java\com\microsoft\example`: contiene i test per l'applicazione.  

### <a name="remove-the-generated-example-code"></a>Rimuovere il codice di esempio generato

Eliminare i file dell'applicazione e il test generato `AppTest.java`, e `App.java` immettendo i comandi seguenti:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Aggiungere archivi Maven

HDInsight si basa su Hortonworks Data Platform (HDP), perciò è consigliabile usare l'archivio Hortonworks per scaricare le dipendenze per i progetti Apache Storm.  

Apri `pom.xml` immettendo il comando seguente:

```cmd
notepad pom.xml
```

Quindi aggiungere il codice XML seguente dopo il `<url> https://maven.apache.org</url>` riga:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Aggiungere le proprietà

Maven consente di definire i valori a livello di progetto denominati proprietà. Nelle `pom.xml`, aggiungere il testo seguente dopo il `</repositories>` riga:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

È ora possibile usare questo valore in altre sezioni del file `pom.xml`. Ad esempio, quando si specifica la versione dei componenti Storm, è possibile usare `${storm.version}` anziché codificare un valore.

## <a name="add-dependencies"></a>Aggiungere le dipendenze

Aggiungere una dipendenza per i componenti Storm. Nelle `pom.xml`, aggiungere il testo seguente nel `<dependencies>` sezione:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

In fase di compilazione, Maven usa queste informazioni per cercare `storm-core` nell'archivio. Viene innanzitutto esaminato il repository del computer locale. Se i file non sono presenti, verranno scaricati tramite Maven dall'archivio pubblico Maven e inseriti nell'archivio locale.

> [!NOTE]  
> Si noti la riga `<scope>provided</scope>` in questa sezione. Questa impostazione indica a Maven di escludere **storm-core** da qualsiasi file con estensione JAR creato, poiché viene fornito dal sistema.

## <a name="build-configuration"></a>Configurare la compilazione

I plug-in di Maven consentono di personalizzare le fasi di compilazione del progetto. Ad esempio, il modo in cui viene compilato il progetto o viene creato il pacchetto come file JAR. Nelle `pom.xml`, aggiungere il testo seguente direttamente sopra il `</project>` riga.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Questa sezione viene usata per aggiungere plug-in, risorse e altre opzioni di configurazione della compilazione. Per un riferimento completo del `pom.xml` file, vedere [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Aggiungere plug-in

* **Plug-in Maven Exec**

    Per le topologie Apache Storm implementate in Java, il [plug-in Exec Maven](https://www.mojohaus.org/exec-maven-plugin/) risulta particolarmente utile in quanto consente di eseguire facilmente la topologia nell'ambiente di sviluppo in uso. Aggiungere quanto segue alla sezione `<plugins>` del file `pom.xml` per includere il plug-in Exec Maven:
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
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
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Plug-in di Apache Maven Compiler**

    Anche il [plug-in Apache Maven Compiler](https://maven.apache.org/plugins/maven-compiler-plugin/), usato per modificare le opzioni di compilazione, è molto utile. Modificare la versione di Java usata da Maven per l'origine e destinazione per l'applicazione.
    
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

La sezione delle risorse consente di includere le risorse non di codice, ad esempio i file di configurazione richiesti dai componenti della topologia. Per questo esempio, aggiungere il testo seguente nel `<resources>` sezione il `pom.xml` file.

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

Una topologia Apache Storm basata su Java è costituita da tre componenti che è necessario creare o a cui è necessario fare riferimento come dipendenza.

* **Spouts**: legge i dati da origini esterne e genera flussi di dati nella topologia.

* **Bolts**: esegue l'elaborazione sui flussi generati dagli spout o da altri bolt e genera uno o più flussi.

* **Topologia**: definisce il modo in cui vengono disposti gli spout e i bolt e fornisce il punto di ingresso per la topologia.

### <a name="create-the-spout"></a>Creare lo spout

Per ridurre i requisiti relativi all'impostazione di origini dati esterne, lo spout seguente genera semplicemente frasi casuali. Si tratta di una versione modificata di uno spout fornito con gli esempi di [Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Anche se questa topologia usa soltanto uno spout, altre topologie possono avere diversi spout che inseriscono dati da diverse origini.

Immettere il comando seguente per creare e aprire un nuovo file `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file.  Quindi chiudere il file.

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

> [!NOTE]  
> Per uno spout in grado di leggere da un'origine dati esterna, vedere uno degli esempi seguenti:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): spout di esempio che legge da Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): spout che legge da Kafka.


### <a name="create-the-bolts"></a>Creare i bolt

I bolt gestiscono l'elaborazione dei dati. I bolt eseguono qualsiasi tipo di attività, ad esempio calcolo, persistenza o comunicazione con componenti esterni. Questa topologia usa due bolt:

* **SplitSentence**: divide le frasi generate da **RandomSentenceSpout** in singole parole.

* **WordCount**: conta le occorrenze di ciascuna parola.


#### <a name="splitsentence"></a>SplitSentence

Immettere il comando seguente per creare e aprire un nuovo file `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file.  Quindi chiudere il file.

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

#### <a name="wordcount"></a>WordCount

Immettere il comando seguente per creare e aprire un nuovo file `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file.  Quindi chiudere il file.

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

### <a name="define-the-topology"></a>Definire la topologia

La topologia collega gli spout e i bolt in un grafico, che definisce il flusso di dati tra i componenti. Fornisce inoltre suggerimenti di parallelismo usati da Storm durante la creazione di istanze di componenti all'interno del cluster.

La seguente immagine è un diagramma di base del grafico dei componenti della topologia.

![diagramma che mostra la disposizione degli spout e dei bolt](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Per implementare la topologia, immettere il comando seguente per creare e aprire un nuovo file `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file.  Quindi chiudere il file.

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

### <a name="configure-logging"></a>Configurare la registrazione

Storm usa [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) per registrare le informazioni. Se non si configura la registrazione, la topologia genera informazioni di diagnostica. Per controllare ciò che viene registrato, creare un file denominato `log4j2.xml` nella `resources` directory immettendo il comando seguente:

```cmd
notepad resources\log4j2.xml
```

Quindi copiare e incollare il testo XML seguente nel nuovo file.  Quindi chiudere il file.

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

Questo XML consente di configurare un nuovo logger per la classe `com.microsoft.example`, che include i componenti in questa topologia di esempio. Il livello è impostato sul monitoraggio per questo logger tramite il quale acquisisce le informazioni di registrazione generate dai componenti in questa topologia.

La sezione `<Root level="error">` configura il livello radice di registrazione, ovvero tutti gli elementi non presenti in `com.microsoft.example`, per poter registrare solo le informazioni relative agli errori.

Per altre informazioni sulla configurazione della registrazione per Log4j 2, vedere [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Storm versione 0.10.0 e successive usano Log4j 2.x. Le versioni precedenti usano Log4j 1.x, che impiega un formato diverso per la configurazione del log. Per informazioni sulla configurazione precedente, vedere [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testare la topologia in locale

Dopo aver salvato i file, usare il comando seguente per testare la topologia in locale.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Durante l'esecuzione, la topologia mostra le informazioni di avvio. Il testo seguente è un esempio di output del conteggio parole:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Questo log di esempio indica che la parola "and" è stata generata 113 volte. Il conteggio continua ad aumentare fintanto che la topologia è in esecuzione perché lo spout emette continuamente le stesse frasi.

C'è un intervallo di 5 secondi tra l'emissione di parole e i conteggi. Il componente **WordCount** è configurato per generare informazioni solo quando arriva una tupla tick. Richiede che le tuple tick vengano recapitate solo ogni cinque secondi.

## <a name="convert-the-topology-to-flux"></a>Convertire la topologia in Flux

[Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) è un nuovo framework disponibile con Storm 0.10.0 e versioni successive, che consente di separare la configurazione dall'implementazione. I componenti sono ancora definiti in Java, ma la topologia viene definita mediante un file YAML. È possibile impacchettare una definizione di topologia predefinita con il progetto o usare un file autonomo per l'invio della topologia. Quando si invia la topologia a Storm, è possibile usare variabili di ambiente o file di configurazione per popolare i valori nella definizione della topologia YAML.

Il file YAML definisce i componenti da usare per la topologia e i dati di flusso tra essi. È possibile includere un file YAML come parte del file con estensione JAR oppure usare un file esterno YAML.

Per altre informazioni su Flux, vedere il [framework Flux (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]  
> A causa di un [bug (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) in Storm 1.0.1, per eseguire localmente le topologie Flux può essere necessario installare un [ambiente di sviluppo Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

1. In precedenza, `WordCountTopology.java` definito la topologia, ma non è necessario con Flux. Eliminare il file con il comando seguente:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. Immettere il comando seguente per creare e aprire un nuovo file `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    Quindi copiare e incollare il testo sottostante nel nuovo file.  Quindi chiudere il file.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Immettere il comando seguente per aprire `pom.xml` per rendere le revisioni descritte sotto:

    ```cmd
    notepad pom.xml
    ```

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
            <version>3.2.1</version>
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

   * Nel **exec-maven-plugin** `<configuration>` sezione, modificare il valore per `<mainClass>` dalla `${storm.topology}` a `org.apache.storm.flux.Flux`. Questa impostazione consente a Flux di gestire l'esecuzione in locale della topologia nell'ambiente di sviluppo.

   * Nel `<resources>` sezione, aggiungere quanto segue a `<includes>`. Questo XML include il file YAML che definisce la topologia come parte del progetto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testare la topologia Flux in locale

1. Immettere il comando seguente per compilare ed eseguire la topologia Flux usando Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Questo comando non va a buon fine se la topologia usa bit di Storm 1.0.1. L'errore è causato da [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055). [Installare Storm nell'ambiente di sviluppo](https://storm.apache.org/releases/current/Setting-up-development-environment.html) e usare la procedura seguente:
    >
    > Se [Storm è installato nell'ambiente di sviluppo](https://storm.apache.org/releases/current/Setting-up-development-environment.html), è possibile usare i comandi seguenti:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Il parametro `--local` esegue la topologia in modalità locale nell'ambiente di sviluppo. Il parametro `-R /topology.yaml` usa la risorsa file `topology.yaml` dal file jar per definire la topologia.

    Durante l'esecuzione, la topologia mostra le informazioni di avvio. Il testo seguente è un esempio di output:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    C'è un ritardo di 10 secondi tra i batch delle informazioni registrate.

2. Creare un nuovo yaml topologia dal progetto.
 
    a. Immettere il comando seguente per aprire `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. Trovare la seguente sezione e modificare il valore della `10` a `5`. Questa modifica consente di cambiare l'intervallo tra i batch di emissione del conteggio di parole da 10 secondi a 5.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. Salva file con nome `newtopology.yaml`.

3. Per eseguire la topologia, immettere il comando seguente:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    In alternativa, se si dispone di Storm nell'ambiente di sviluppo:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     Questo comando Usa il `newtopology.yaml` come definizione della topologia. Siccome il parametro `compile` non è stato incluso, Maven usa la versione del progetto creato nei passaggi precedenti.

    Una volta avviata la topologia, è possibile notare che il tempo tra i batch emessi è cambiato per riflettere il valore in `newtopology.yaml`. Pertanto, è possibile modificare la configurazione tramite un file YAML senza dover ricompilare la topologia.

Per altre informazioni su queste e altre funzionalità del framework Flux, vedere [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) è un'astrazione di alto livello fornita da Storm che supporta l'elaborazione con informazioni sullo stato. Il principale vantaggio offerto da Trident è la garanzia che ogni messaggio introdotto nella topologia viene elaborato una sola volta. Senza l'uso di Trident, la topologia può garantire solo che i messaggi vengono elaborati almeno una volta. Esistono altre differenze, ad esempio la disponibilità di componenti predefiniti che possono essere usati senza che sia necessario creare bolt. I bolt sono infatti sostituiti da componenti meno generici, ad esempio filtri, proiezioni e funzioni.

È possibile creare applicazioni Trident mediante progetti Maven L'unica differenza consiste nel codice. Trident, inoltre, non è (attualmente) utilizzabile con il framework Flux.

Per altre informazioni su Trident, vedere la [panoramica dell'API Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Fasi successive

A questo punto, dopo aver appreso come creare una topologia Apache Storm con Java, è possibile passare agli argomenti seguenti:

* [Distribuzione e gestione di topologie Apache Storm in HDInsight](apache-storm-deploy-monitor-topology.md)

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Per altri esempi di topologie Apache Storm, vedere [Topologie di esempio per Apache Storm in HDInsight](apache-storm-example-topology.md).
