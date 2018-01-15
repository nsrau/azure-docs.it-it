---
title: Topologie Apache Storm di esempio in HDInsight | Documentazione Microsoft
description: Un elenco di esempi di topologie Storm create e testate con Apache Storm in HDInsight, incluse le topologie C# e Java di base per l'utilizzo di hub eventi.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: c6c1a1483191e654c245eb3f05ee9e8406510b08
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Esempi di topologie e componenti Storm per Apache Storm in HDInsight

Di seguito è riportato un elenco di esempi creati e gestiti da Microsoft per l'uso con Apache Storm in HDInsight. Questi esempi riguardano numerosi argomenti, dalla creazione di topologie C# e Java di base, all'uso dei servizi di Azure come Hub eventi, Cosmos DB, database SQL, HBase in HDInsight e Archiviazione di Azure. Alcuni esempi illustrano anche come usare le tecnologie non Azure o addirittura non Microsoft, quali SignalR e Socket.IO.

| Descrizione | Dimostra | Linguaggio/framework |
|:--- |:--- |:--- |
| [Scrivere in Archivio Azure Data Lake da Apache Storm](apache-storm-write-data-lake-store.md) |Scrittura in Archivio Azure Data Lake |Java |
| [Origine per Spout e Bolt dell'hub eventi](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Origine per Spout e Bolt dell'hub eventi |Java |
| [Sviluppare topologie basate su Java per Apache Storm in HDInsight][5797064f] |Maven |Java |
| [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio][16fce2d1] |HDInsight Tools per Visual Studio |C#, Java |
| [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)][844d1d81] |Hub eventi |C# e Java |
| [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Hub eventi |Java |
| [Elaborare i dati del sensore veicolo dall'hub di eventi usando Storm in HDInsight][246ee964] |Hub eventi, Cosmos DB, BLOB del servizio di archiviazione di Azure (WASB) |C#, Java |
| [Estrarre, trasformare e caricare da hub eventi di Azure in HBase usando Storm in HDInsight][b4b68194] |Hub eventi, HBase |C# |
| [Progetto di topologia Storm C# modello per l'uso dei servizi Azure da Storm in HDInsight][ce0c02a2] |Hub eventi, Cosmos DB, database SQL, HBase, SignalR |C#, Java |
| [Benchmark di scalabilità per la lettura da hub eventi di Azure con Storm in HDInsight][d6c540e3] |Velocità effettiva dei messaggi, hub di eventi, database SQL |C#, Java |
| [Introduzione a Python con Storm in HDInsight](apache-storm-develop-python-topology.md) |Componenti Python con una topologia Flux |Python |
| [Usare Kafka con Storm in HDInsight](../hdinsight-apache-storm-with-kafka.md) | Lettura e scrittura di Apache Storm in Apache Kafka | Java |

### <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Apache Storm in HDInsight][2b8c3488]
* [Informazioni sulla distribuzione e la gestione di topologie Storm con Storm in HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Informazioni sulla creazione di una topologia Storm in un cluster HDInsight e l'uso del Dashboard di Storm per distribuire topologie di esempio."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Informazioni su come distribuire e gestire topologie usando il Dashboard di Storm basato su Web e l'interfaccia utente di Storm oppure gli strumenti di HDInsight per Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Informazioni su come creare topologie Storm C# usando gli strumenti di HDInsight per Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Informazioni su come creare topologie Storm in Java, usando Maven e creando una topologia di conteggio parole di base."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Informazioni su come leggere e scrivere dati di hub eventi di Azure con Storm in HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Informazioni su come usare una topologia Storm per la lettura di messaggi dagli hub eventi di Azure, la lettura di documenti da Azure Cosmos DB per il riferimento ai dati e il salvataggio dei dati in Archiviazione di Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varie topologie per illustrare la velocità effettiva durante la lettura dagli hub eventi di Azure e l'archiviazione nel database SQL usando Apache Storm in HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Informazioni su come leggere dati dagli hub eventi di Azure, aggregare e trasformare i dati e quindi archiviarli in HBase in HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Questo progetto contiene modelli per l'interazione di spout, bolt e topologie con diversi servizi Azure come hub eventi, Cosmos DB e database SQL."

