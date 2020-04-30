---
title: Guida per gli sviluppatori Apache Kafka per hub eventi
description: Questo articolo fornisce collegamenti ad articoli che descrivono come integrare le applicazioni Kafka con hub eventi di Azure.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729638"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Guida per gli sviluppatori Apache Kafka per hub eventi di Azure
Questo articolo fornisce collegamenti ad articoli che descrivono come integrare le applicazioni Apache Kafka con hub eventi di Azure. 

## <a name="overview"></a>Panoramica
Hub eventi fornisce un endpoint Kafka che può essere usato dalle applicazioni Kafka esistenti come alternativa all'esecuzione del proprio cluster Kafka. Hub eventi supporta il protocollo Apache Kafka 1.0 e versioni successive e funziona con le applicazioni Kafka esistenti, tra cui MirrorMaker. Per ulteriori informazioni, vedere [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Avvi rapidi
È possibile trovare guide introduttive in GitHub e in questo set di contenuti che consente di accedere rapidamente a hub eventi per Kafka.

### <a name="quickstarts-in-github"></a>Guide introduttive in GitHub
Vedere le guide introduttive seguenti nel repository **Azure-Event-Hub-for-Kafka** : 

| Lingua/Framework client | Descrizione | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in C# con .NET Core 2,0.</p><p>Questo esempio si basa sul [client Apache Kafka .NET di Confluent](https://github.com/confluentinc/confluent-kafka-dotnet), modificato per l'uso con hub eventi per Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti nel nodo.</p><p>In questo esempio viene utilizzata la libreria [node-rdkafka](https://github.com/Blizzard/node-rdkafka) . </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in Python.</p><p>Questo esempio è basato sul [client Python Apache Kafka di Confluent](https://github.com/confluentinc/confluent-kafka-python), modificato per l'uso con hub eventi per Kafka.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in go.</p><p>Questo esempio si basa sul [client di Apache Kafka Golang di Confluent](https://github.com/confluentinc/confluent-kafka-go), modificato per l'uso con hub eventi per Kafka.</p>| 
| [Sarama Kafka go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in go usando la libreria [client di sarama Kafka](https://github.com/Shopify/sarama) . |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Questa Guida introduttiva illustra come creare e connettersi a un endpoint Kafka di hub eventi usando l'interfaccia della riga di comando fornita in bundle con la distribuzione di Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat è un consumer della riga di comando non JVM e Producer basato su librdkafka, molto diffuso a causa della velocità e del footprint ridotto. Questa Guida introduttiva contiene una configurazione di esempio e alcuni semplici comandi di kafkacat di esempio. | 
 
### <a name="quickstarts-in-docs"></a>Guide introduttive in DOCS
Vedere la Guida introduttiva: [flusso di dati con hub eventi usando il protocollo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) in questo set di contenuti, che fornisce istruzioni dettagliate su come eseguire lo streaming in hub eventi. Si apprenderà come usare i producer e i consumer per comunicare con Hub eventi con solo una modifica della configurazione nelle applicazioni. 


## <a name="tutorials"></a>Esercitazioni 

### <a name="tutorials-in-github"></a>Esercitazioni in GitHub
Vedere le esercitazioni seguenti su GitHub:

| Esercitazione | Descrizione | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Questa esercitazione illustra come connettere Akka Streams a hub eventi abilitati per Kafka senza modificare i client del protocollo o eseguire i propri cluster. Sono disponibili due esercitazioni separate che usano i linguaggi di programmazione **Java** e **scala** . | 
| [Connettere](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Questo documento illustra l'integrazione di Kafka Connect con hub eventi di Azure e la distribuzione di connettori di base FileStreamSource e FileStreamSink. Sebbene questi connettori non siano destinati all'uso in ambiente di produzione, dimostrano uno scenario di connessione Kafka end-to-end in cui l'hub eventi di Azure si maschera come broker Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Questo documento illustra l'integrazione di filebeat e hub eventi tramite l'output Kafka di filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Questa esercitazione illustra come connettere Apache flink a hub eventi abilitati per Kafka senza modificare i client del protocollo o eseguire i propri cluster. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Questo documento illustra come integrare Fluentd e hub eventi usando il `out_kafka` plug-in di output per fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Questa esercitazione illustra come scambiare eventi tra consumer e Producer usando protocolli diversi. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Questa esercitazione illustra l'integrazione di logstash con hub eventi abilitati per Kafka con i plug-in di input/output di logstash Kafka. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Questa esercitazione illustra come un hub eventi e Kafka MirrorMaker possono integrare una pipeline Kafka esistente in Azure eseguendo il mirroring del flusso di input Kafka nel servizio Hub eventi. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | In questa esercitazione viene illustrato come connettere Apache NiFi a uno spazio dei nomi di hub eventi. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Le guide introduttive mostrano come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in linguaggi di programmazione Go e Java. |
| [Registro dello schema di Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Questa esercitazione illustra l'integrazione del registro dello schema e di hub eventi per Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Questa esercitazione illustra come connettere l'applicazione Spark a un hub eventi senza modificare i client del protocollo o eseguire i cluster Kafka. | 

### <a name="tutorials-in-docs"></a>Esercitazioni in DOCS
Vedere anche l'esercitazione: [elaborare Apache Kafka per gli eventi di hub eventi usando analisi di flusso](event-hubs-kafka-stream-analytics.md) in questo set di contenuti, che Mostra come trasmettere i dati in hub eventi ed elaborarli con analisi di flusso di Azure.

## <a name="how-to-guides"></a>Guide pratiche
Vedere le guide alle procedure seguenti nella documentazione di:

| Articolo | Descrizione | 
| ------- | ----------- | 
| [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md) | Illustra come eseguire il mirroring di un broker Kafka in un hub eventi usando Kafka MirrorMaker. |
| [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md) | Viene illustrato come connettere l'applicazione Spark a hub eventi per lo streaming in tempo reale. |
| [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md) | Mostra come connettere Apache flink a un hub eventi senza modificare i client del protocollo o eseguire i propri cluster. |
| [Integrare Apache Kafka connettersi con un hub eventi (anteprima)](event-hubs-kafka-connect-tutorial.md) | Viene illustrata l'integrazione di Kafka Connect con un hub eventi e la distribuzione di connettori di base FileStreamSource e FileStreamSink. |
| [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md) | Mostra come connettere i flussi Akka a un hub eventi senza modificare i client del protocollo o eseguire i propri cluster. |
| [Usare Spring boot Starter per Apache Kafka con hub eventi di Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Viene illustrato come configurare uno strumento di associazione di flussi cloud di Spring basato su Java creato con l'inizializzatore Spring boot per usare Apache Kafka con hub eventi di Azure. |

## <a name="next-steps"></a>Passaggi successivi
Esaminare gli esempi nel repository GitHub [Azure-Event-Hub-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) in guide introduttive ed esercitazioni.

Vedere anche gli articoli seguenti:

- [Guida alla risoluzione dei problemi di Apache Kafka per hub eventi](apache-kafka-troubleshooting-guide.md)
- [Domande frequenti-Hub eventi per Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guida alla migrazione di Apache Kafka per hub eventi](apache-kafka-migration-guide.md)



