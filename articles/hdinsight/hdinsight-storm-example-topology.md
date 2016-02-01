<properties
 pageTitle="Topologie Apache Storm di esempio in HDInsight | Microsoft Azure"
 description="Un elenco di esempi di topologie Storm create e testate con Apache Storm in HDInsight, incluse le topologie C# e Java di base per l'utilizzo di hub eventi."
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
 ms.date="01/15/2016"
 ms.author="larryfr"/>

# Esempi di topologie e componenti Storm per Apache Storm in HDInsight

Di seguito è riportato un elenco di esempi creati e gestiti da Microsoft per l'uso con Apache Storm in HDInsight. Questi esempi coprono un'ampia gamma di argomenti, dalla creazione di codice C# di base e topologie Java all'uso dei servizi di Azure come hub di eventi, DocumentDB, Power BI, Database SQL, HBase in HDInsight e archiviazione di Azure. Alcuni esempi illustrano anche come usare le tecnologie non Azure o addirittura non Microsoft, quali SignalR e Socket.IO

| Descrizione | Dimostra | Linguaggio/framework |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Scrivere in Archivio Azure Data Lake da Apache Storm](hdinsight-storm-write-data-lake-store.md) | Scrittura in Archivio Azure Data Lake | Java |
| [Origine per Spout e Bolt dell'hub eventi](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Origine per Spout e Bolt dell'hub eventi | Java |
| [Sviluppo di topologie basate su Java per Apache Storm in HDInsight][5797064f] | Maven | Java |
| [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio][16fce2d1] | HDInsight Tools per Visual Studio | C#, Java |
| [Creare più flussi di dati in una topologia Storm C#][ec5a4064] | Più flussi | C# |
| [Temi di tendenza Twitter con Apache Storm in HDInsight][3c86c7c8] | Trident | Java, Trident |
| [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)][844d1d81] | Hub eventi | C# e Java |
| [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Hub eventi | Java |
| [Usare Power BI (anteprima) per visualizzare i dati da una topologia Storm][94d15238] | Power BI | C# |
| [Analisi dei dati dei sensori con Storm e HBase in HDInsight][ab894747] | Hub eventi, HBase, Socket.IO, dashboard Web | C#, Java, JavaScript, HTML |
| [Elaborare i dati del sensore veicolo dall'hub di eventi usando Storm in HDInsight][246ee964] | Hub eventi, DocumentDb, BLOB di Archiviazione di Azure (WASB) | C#, Java |
| [Estrarre, trasformare e caricare dagli hub eventi di Azure in HBase usando Storm in HDInsight][b4b68194] | Hub eventi, HBase | C# |
| [Progetto di topologia Storm C# modello per l'uso dei servizi Azure da Storm in HDInsight][ce0c02a2] | Hub eventi, DocumentDB, database SQL, HBase, SignalR | C#, Java |
| [Benchmark di scalabilità per la lettura da hub di eventi di Azure mediante Storm in HDInsight][d6c540e3] | Velocità effettiva dei messaggi, hub di eventi, database SQL | C#, Java |
| [Correlare gli eventi con Storm e HBase in HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Introduzione a Python con Storm in HDInsight](hdinsight-storm-develop-python-topology.md) | I componenti di Python con Java e Clojure basati su topologie Storm | Python |

## Passaggi successivi

* [Introduzione ad Apache Storm in HDInsight][2b8c3488]

* [Informazioni su come distribuire e gestire le topologie Storm con Storm in HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Informazioni su come creare una topologia Storm nel cluster HDInsight e usare Storm Dashboard per distribuire le topologie di esempio."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Informazioni su come distribuire e gestire le topologie usando Storm Dashboard basato su Web e l'interfaccia utente Storm oppure HDInsight Tools per Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Informazioni su come creare topologie Storm C# usando HDInsight Tools per Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Informazioni su come creare topologie Storm in Java, usando Maven e creando una topologia di conteggio parole di base."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Illustra come scrivere dati in Power BI da una topologia C#, quindi creare un grafico e un dashboard in base ai dati."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Illustra una topologia Storm di base che esegue un conteggio di parole, implementata in C#. Ciò dimostra anche come creare più flussi di dati all'interno di una topologia C#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Informazioni su come leggere e scrivere dati dall'hub eventi di Azure con Storm in HDInsight"
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Informazioni su come usare Apache Storm in HDInsight per elaborare i dati del sensore dagli hub di eventi di Azure, visualizzarli con D3.js e (facoltativamente) archiviarli in HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Informazioni su come usare Trident per creare una topologia Storm in grado di determinare i temi di tendenza in base agli hashtag su Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Informazioni su come usare una topologia Storm per la lettura di messaggi dagli hub eventi di Azure, la lettura di documenti da Azure DocumentDB per il riferimento ai dati e il salvataggio dei dati in Archiviazione di Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varie topologie per illustrare la velocità effettiva durante la lettura dagli hub eventi di Azure e l'archiviazione nel database SQL usando Apache Storm in HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Informazioni su come leggere dati dagli hub eventi di Azure, aggregare e trasformare i dati, quindi archiviarli in HBase in HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Questo progetto contiene modelli per l'interazione di Spout, Bolt e topologie con diversi servizi Azure come hub eventi, DocumentDB e database SQL."
 

<!---HONumber=AcomDC_0121_2016-->