---
title: Esempio di topologie Apache Storm in Azure HDInsight
description: Un elenco di esempi di topologie Storm create e testate con Apache Storm in HDInsight, incluse le topologie C# e Java di base per l'utilizzo di hub eventi.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: b2407200b995ad956f233248f6cdd58488b583d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688798"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Esempi di topologie e componenti Apache Storm per Apache Storm in HDInsight

Di seguito è riportato un elenco di esempi creati e gestiti da Microsoft per l'uso con [Apache Storm](https://storm.apache.org/) in HDInsight. Questi esempi riguardano numerosi argomenti, dalla creazione di topologie C# e Java di base, all'uso dei servizi di Azure come Hub eventi, Cosmos DB, database SQL, [Apache HBase](https://hbase.apache.org/) in HDInsight e Archiviazione di Azure. Alcuni esempi illustrano anche come usare le tecnologie non Azure o addirittura non Microsoft, quali SignalR e Socket.IO.

| DESCRIZIONE | Dimostra | Linguaggio/framework |
|:--- |:--- |:--- |
| [Scrivere in Azure Data Lake Storage da Apache Storm](apache-storm-write-data-lake-store.md) |Scrittura in Azure Data Lake Storage |Java |
| [Origine per Spout e Bolt dell'hub eventi](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Origine per Spout e Bolt dell'hub eventi |Java |
| [Sviluppare topologie basate su Java per Apache Storm in HDInsight][5797064f] |Maven |Java |
| [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio][16fce2d1] |HDInsight Tools per Visual Studio |C#, Java |
| [Elaborare eventi di Hub eventi di Azure con Apache Storm in HDInsight (C#)][844d1d81] |Hub eventi |C# e Java |
| [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Hub eventi |Java |
| [Elaborare i dati del sensore veicolo da Hub eventi di Azure usando Apache Storm in HDInsight][246ee964] |Hub eventi, Cosmos DB, BLOB del servizio di archiviazione di Azure (WASB) |C#, Java |
| [Estrarre, trasformare e caricare da Hub eventi di Azure in Apache HBase usando Apache Storm in HDInsight][b4b68194] |Hub eventi, HBase |C# |
| [Progetto di topologia Storm C# modello per l'uso dei servizi Azure da Apache Storm in HDInsight][ce0c02a2] |Hub eventi, Cosmos DB, database SQL, HBase, SignalR |C#, Java |
| [Benchmark di scalabilità per la lettura da Hub eventi di Azure con Apache Storm in HDInsight][d6c540e3] |Velocità effettiva dei messaggi, hub di eventi, database SQL |C#, Java |
| [Introduzione a Python con Apache Storm in HDInsight](apache-storm-develop-python-topology.md) |Componenti Python con una topologia Flux |Python |
| [Usare Apache Kafka con Apache Storm in HDInsight](../hdinsight-apache-storm-with-kafka.md) | Lettura e scrittura di Apache Storm in Apache Kafka | Java |

> [!WARNING]  
> Gli esempi in C# in questo elenco sono stati originariamente creati e testati con HDInsight basato su Windows e potrebbero non funzionare correttamente con i cluster HDInsight basati su Linux. I cluster basati su Linux usano Mono per eseguire il codice .NET e potrebbero presentare problemi di compatibilità con il framework e i pacchetti usati nell'esempio.
>
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva.

### <a name="next-steps"></a>Fasi successive

* [Introduzione ad Apache Storm in HDInsight][2b8c3488]
* [Informazioni sulla distribuzione e la gestione di topologie Apache Storm con Apache Storm in HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Informazioni sulla creazione di una topologia Apache Storm in un cluster HDInsight e l'uso del Dashboard di Storm per distribuire topologie di esempio."
[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Informazioni su come distribuire e gestire topologie usando il Dashboard di Apache Storm basato su Web e l'interfaccia utente di Storm oppure gli strumenti di HDInsight per Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Informazioni su come creare topologie Storm C# usando gli strumenti di HDInsight per Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Informazioni su come creare topologie Storm in Java, usando Maven e creando una topologia di conteggio parole di base."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Informazioni su come leggere e scrivere dati di hub eventi di Azure con Storm in HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Informazioni su come usare una topologia Storm per la lettura di messaggi dagli hub eventi di Azure, la lettura di documenti da Azure Cosmos DB per il riferimento ai dati e il salvataggio dei dati in Archiviazione di Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varie topologie per illustrare la velocità effettiva durante la lettura dagli hub eventi di Azure e l'archiviazione nel database SQL usando Apache Storm in HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Informazioni su come leggere dati dagli hub eventi di Azure, aggregare e trasformare i dati e quindi archiviarli in HBase in HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Questo progetto contiene modelli per l'interazione di spout, bolt e topologie con diversi servizi Azure come hub eventi, Cosmos DB e database SQL."

