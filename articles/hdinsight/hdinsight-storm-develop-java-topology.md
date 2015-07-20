<properties
   pageTitle="Sviluppare topologie basate su Java per Apache Storm | Microsoft Azure"
   description="Informazioni su come creare topologie Storm in Java mediante la creazione di una semplice topologia di conteggio parole."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/08/2015"
   ms.author="larryfr"/>

#Sviluppare topologie basate su Java per un'applicazione di base per il conteggio di parole con Apache Storm e Maven in HDInsight

Informazioni sul processo di base per la creazione di una topologia basata su Java per Apache Storm in HDInsight usando Maven. Viene illustrato in dettaglio il processo di creazione di un'applicazione di conteggio parole di base mediante Maven e Java. Anche se le istruzioni fornite sono pensate per l'uso con Eclipse, è comunque possibile usare un editor di testo di propria scelta.

Dopo aver completato i passaggi descritti in questo documento, si disporrà di una topologia di base che è possibile distribuire ad Apache Storm in HDInsight.

##Prerequisiti

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) versione 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven è un sistema di compilazione per progetti Java.

* Un editor di testo, ad esempio Blocco note, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a> o <a href="http://brackets.io/" target="_blank">Brackets.io</a>. In alternativa, è possibile usare un ambiente di sviluppo integrato (IDE) come <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versione Luna o successiva).

	> [AZURE.NOTE]È possibile che l'editor di testo o l'ambiente IDE offra funzionalità specifiche per l'uso di Maven non descritte in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto usato.

##Configurare le variabili di ambiente

Quando si installa Java e JDK, è possibile impostare le variabili di ambiente indicate di seguito. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: deve contenere i percorsi seguenti:

	* **JAVA_HOME** o il percorso equivalente

	* **JAVA_HOME\\bin** o il percorso equivalente

	* Directory in cui è installato Maven

##Creare un nuovo progetto Maven

Dalla riga di comando usare il codice seguente per creare un nuovo progetto Maven denominato **WordCount**:

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Nella posizione corrente verrà creata una nuova directory denominata **WordCount**, contenente un progetto Maven di base.

La directory **WordCount** conterrà gli elementi seguenti:

* **pom.xml**: contiene le impostazioni per il progetto Maven

* **src\\main\\java\\com\\microsoft\\example**: contiene il codice dell'applicazione.

* **src\\test\\java\\com\\microsoft\\example**: contiene i test per l'applicazione. Per questo esempio non verranno creati test.

###Rimuovere il codice di esempio

Poiché si sta creando una propria applicazione, eliminare il test e i file dell'applicazione generati:

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.java**

*  **src\\main\\java\\com\\microsoft\\example\\App.java**

##Aggiungere le dipendenze

Poiché si tratta di una topologia Storm, è necessario aggiungere una dipendenza per i componenti Storm. Aprire il file **pom.xml** e aggiungere quanto segue nella sezione **&lt;dependencies>**.

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

In fase di compilazione, Maven usa queste informazioni per cercare **storm-core** nel repository. Viene innanzitutto esaminato il repository del computer locale. Se i file non sono presenti, verranno scaricati dal repository pubblico Maven e archiviati nel repository locale.

> [AZURE.NOTE]Si noti la riga `<scope>provided</scope>` nella sezione aggiunta. Tale riga indica a Maven di escludere **storm-core** da qualsiasi file con estensione jar creato, poiché storm-core verrà fornito dal sistema. Questo assicura che i pacchetti creati saranno di dimensioni più ridotte e che useranno i bit **storm-core** inclusi nel cluster Storm in HDInsight.

##Configurare la compilazione

I plug-in Maven consentono di personalizzare le fasi di compilazione di un progetto, ad esempio il modo in cui tale progetto viene compilato o inserito come pacchetto in un file con estensione jar. Aprire il file **pom.xml** e aggiungere quanto segue direttamente sopra la riga `</project>`.

	<build>
	  <plugins>
	  </plugins>
	</build>

Questa sezione verrà usata per aggiungere plug-in e altre opzioni di configurazione della compilazione.

###Aggiungere plug-in

Per le topologie Storm, il <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">plug-in Exec Maven</a> risulta particolarmente utile in quanto consente di eseguire facilmente la topologia nell'ambiente di sviluppo in uso. Aggiungere quanto segue alla sezione `<plugins>` del file **pom.xml** per includere il plug-in Exec Maven.

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
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

Anche il <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">plug-in Apache Maven Compiler</a>, usato per modificare le opzioni di compilazione, è molto utile. Questo plug-in è necessario innanzitutto perché consente di modificare la versione di Java usata da Maven come versione di origine e destinazione dell'applicazione. La versione richiesta è la 1.7.

Aggiungere quanto segue alla sezione `<plugins>` del file **pom.xml** per includere il plug-in Apache Maven Compiler e impostare la versione di origine e destinazione su 1.7.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##Creare la topologia

Una topologia Storm basata su Java è costituita da tre componenti che è necessario creare o a cui è necessario fare riferimento come dipendenza.

* **Spout**: legge i dati da origini esterne e genera flussi di dati nella topologia.

* **Bolt**: esegue l'elaborazione sui flussi generati dagli spout o da altri bolt e genera uno o più flussi.

* **Topologia**: definisce il modo in cui vengono disposti gli spout e i bolt e fornisce il punto di ingresso per la topologia.

###Creare lo spout

Per ridurre i requisiti relativi all'impostazione di origini dati esterne, lo spout seguente genera semplicemente frasi casuali. Si tratta di una versione modificata di uno spout fornito con gli (esempi di <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">Storm-Starter</a>).

> [AZURE.NOTE]Per uno spout in grado di leggere da un'origine dati esterna, vedere uno degli esempi seguenti:
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a>: spout di esempio che legge da Twitter
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm-Kafka</a>: spout che legge da Kafka

Per lo spout, creare un nuovo file denominato **RandomSentenceSpout.java** nella directory **src\\main\\java\\com\\microsoft\\example** e usare come contenuto quanto riportato di seguito:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

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
        //The sentences that will be randomly emitted
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

Si consiglia di leggere con attenzione i commenti del codice per comprendere il funzionamento dello spout.

> [AZURE.NOTE]Anche se questa topologia usa soltanto uno spout, altre topologie possono avere diversi spout che inseriscono dati da diverse origini.

###Creare i bolt

I bolt gestiscono l'elaborazione dei dati. Per questa topologia ne vengono usati due:

* **SplitSentence**: divide le frasi generate da **RandomSentenceSpout** in singole parole.

* **WordCount**: conta le occorrenze di ciascuna parola.

> [AZURE.NOTE]I bolt eseguono qualsiasi tipo di attività, ad esempio calcolo, persistenza o comunicazione con componenti esterni.

Creare due nuovi file, **SplitSentence.java** e **WordCount.Java**, nella directory **src\\main\\java\\com\\microsoft\\example**. Usare come contenuto dei file quanto riportato di seguito:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
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

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

Si consiglia di leggere con attenzione i commenti del codice per comprendere il funzionamento di ogni bolt.

###Creare la topologia

La topologia collega gli spout e i bolt in un grafico, che definisce il flusso di dati tra i componenti. Fornisce inoltre suggerimenti di parallelismo usati da Storm durante la creazione di istanze di componenti all'interno del cluster.

Di seguito è riportato un diagramma di base del grafico dei componenti della topologia.

![diagramma che mostra la disposizione degli spout e dei bolt](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Per implementare la topologia, creare un nuovo file denominato **WordCountTopology.java** nella directory **src\\main\\java\\com\\microsoft\\example**. Usare come contenuto del file quanto riportato di seguito:

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

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
        conf.setDebug(true);

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

Si consiglia di leggere con attenzione i commenti del codice per comprendere in che modo la topologia viene definita e quindi inviata al cluster.

##Testare la topologia in locale

Dopo aver salvato i file, usare il comando seguente per testare la topologia in locale.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Durante l'esecuzione, la topologia visualizza informazioni di avvio, quindi, quando lo spout genera le frasi e il bolt le elabora, inizia a visualizzare righe simili alle seguenti.

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

Come si vede dall'output, si è verificato quanto segue:

1. Lo spout genera la frase "an apple a day keeps the doctor away".

2. Il bolt SplitSentence inizia a generare singole parole della frase.

3. Il bolt WordCount inizia a generare le singole parole e le relative occorrenze.

Guardando i dati di output del bolt WordCount, si nota che la parola 'apple' è stata generata 53 volte. Il conteggio continuerà a essere incrementato per tutto il tempo per cui la topologia è in esecuzione, dal momento che le stesse frasi vengono generate più volte in modo casuale e il conteggio non viene mai reimpostato.

##Trident

Trident è un'astrazione di alto livello fornita da Storm che supporta l'elaborazione con informazioni sullo stato. Il principale vantaggio offerto da Trident è la garanzia che ogni messaggio introdotto nella topologia viene elaborato una sola volta. Questo risultato è difficile da ottenere in una topologia Java di tipo raw, che garantisce che i messaggi verranno elaborati almeno una volta. Esistono altre differenze, ad esempio la disponibilità di componenti predefiniti che possono essere usati senza che sia necessario creare bolt. I bolt sono infatti sostituiti da componenti meno generici, ad esempio filtri, proiezioni e funzioni.

È possibile creare applicazioni Trident mediante progetti Maven che usano gli stessi passaggi di base descritti in precedenza. L'unica differenza consiste nel codice.

Per altre informazioni su Trident, vedere la <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">panoramica dell'API Trident</a>.

Per un'applicazione Trident di esempio, vedere [Temi di tendenza Twitter con Apache Storm in HDInsight](hdinsight-storm-twitter-trending.md).

##Passaggi successivi

A questo punto, dopo aver appreso come creare una topologia Storm con Java, è possibile passare agli argomenti seguenti:

* [Distribuzione e gestione di topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Per altri esempi di topologie Storm, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=July15_HO2-->