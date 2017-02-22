---
title: Ricevere eventi da Hub eventi di Azure usando Apache Storm | Microsoft Docs
description: Avviare la ricezione da Hub eventi usando Apache Storm
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: 8db864801042170fcad947d5b40220eea9c1c307

---

## <a name="receive-events-from-azure-event-hubs-using-apache-storm"></a>Ricevere eventi da Hub eventi di Azure usando Apache Storm
[Apache Storm](https://storm.incubator.apache.org) è un sistema distribuito di calcolo in tempo reale che semplifica l'elaborazione affidabile di flussi di dati non associati. Questa sezione illustra come usare uno Storm Spout di Hub eventi per ricevere eventi da Hub eventi stesso. Usando Apache Storm, è possibile dividere gli eventi tra più processi ospitati in nodi diversi. L'integrazione di Hub eventi con Storm semplifica l'uso degli eventi eseguendo il checkpoint trasparente dello stato di avanzamento grazie all'installazione di Zookeeper di Storm e alla gestione dei checkpoint persistenti e delle ricezioni parallele dagli hub eventi.

Per altre informazioni sui modelli di ricezione di Hub eventi, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione usa un'installazione di [HDInsight Storm][HDInsight Storm] , fornita con lo Spout di Hub eventi già disponibile.

1. Seguire la procedura indicata nell' [introduzione a HDInsight Storm](../hdinsight/hdinsight-storm-overview.md) per creare un nuovo cluster HDInsight e quindi connettersi a quest'ultimo tramite Desktop remoto.
2. Copiare il file `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` nell'ambiente di sviluppo locale. Il file contiene il componente events-storm-spout.
3. Usare il comando seguente per installare il pacchetto nell'archivio Maven locale. Ciò consentirà di aggiungerlo come riferimento nel progetto Storm in un passaggio successivo.

```shell
        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
```
4. In Eclipse creare un nuovo progetto Maven, fare clic su **File**, **New** (Nuovo) e infine su **Project** (Progetto).
   
    ![][12]
5. Selezionare **Use default Workspace location** (Usa percorso predefinito dello spazio di lavoro) e quindi fare clic su **Next** (Avanti)
6. Selezionare l'archetipo **maven-archetype-quickstart** e quindi fare clic su **Next** (Avanti)
7. Inserire un valore per **GroupId** e **ArtifactId** e quindi fare clic su **Finish** (Fine)
8. In **pom.xml** aggiungere le dipendenze seguenti nel nodo `<dependency>`.
```xml  
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>
```
9. Nella cartella **src** creare un file denominato **Config.properties** e copiarvi il contenuto seguente, sostituendo questi valori:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Il valore per **eventhub.receiver.credits** determina quanti eventi vengono inseriti in batch prima del loro rilascio nella pipeline di Storm. Per semplicità, questo esempio imposta il valore su 10. In produzione dovrebbe essere impostato su valori superiori, ad esempio 1024.
10. Creare una nuova classe denominata **LoggerBolt** con il codice seguente:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Questo Bolt Storm registra il contenuto degli eventi ricevuti. Può essere facilmente esteso per archiviare tuple in un servizio di archiviazione. L' [esercitazione di analisi dei sensori con HDInsight] usa lo stesso approccio per archiviare dati in HBase.
11. Creare una classe denominata **LogTopology** con il codice seguente:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Questa classe crea un nuovo spout di Hub eventi, usando le proprietà contenute nel file di configurazione, per crearne un'istanza. È importante notare che questo esempio crea tante attività Spout quante sono le partizioni nell'hub eventi, in modo da usare il massimo parallelismo consentito dall'hub eventi stesso.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[esercitazione di analisi dei sensori con HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png


<!--HONumber=Feb17_HO1-->


