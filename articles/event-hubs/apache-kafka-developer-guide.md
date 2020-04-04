---
title: Guida per gli sviluppatori di Apache Kafka per gli hub eventi
description: Questo articolo fornisce collegamenti ad articoli che descrivono come integrare le applicazioni Kafka con hub eventi di Azure.This article provides links to articles that describe how to integrate your Kafka applications with Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 8a72d20101aacaf59b4be5c4a231b132237113f3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633918"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka developer guide for Azure Event Hubs
Questo articolo fornisce collegamenti ad articoli che descrivono come integrare le applicazioni Apache Kafka con gli hub eventi di Azure.This article provides links to articles that describe how to integrate your Apache Kafka applications with Azure Event Hubs. 

## <a name="overview"></a>Panoramica
Hub eventi fornisce un endpoint Kafka che può essere usato dalle applicazioni Kafka esistenti come alternativa all'esecuzione del proprio cluster Kafka. Hub eventi supporta il protocollo Apache Kafka 1.0 e versioni successive e funziona con le applicazioni Kafka esistenti, tra cui MirrorMaker. Per altre informazioni, vedere Hub di eventi per Apache KafkaFor more information, see [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Avvi rapidi
Puoi trovare le guide introduttive in GitHub e in questo set di contenuti che ti aiuta ad aumentare rapidamente gli hub eventi per Kafka.

### <a name="quickstarts-in-github"></a>Guide introduttive in GitHubQuickstarts in GitHub
Vedere le guide introduttive seguenti nel repository **azure-event-hubs-for-kafka:See the following quickstarts in the azure-event-hubs-for-kafka** repo: 

| Linguaggio/framework del client | Descrizione | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Kafka di Hub eventi usando un producer di esempio e un consumer scritto in C, usando .NET Core 2.0.</p><p>Questo esempio è basato sul [client Apache Kafka .NET di Confluent,](https://github.com/confluentinc/confluent-kafka-dotnet)modificato per l'utilizzo con hub eventi per Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Event Hubs Kafka utilizzando un produttore di esempio e un consumer scritto in Java.This quickstart will show how to create and connect to an Event Hubs Kafka endpoint using an example producer and consumer written in Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Kafka di hub eventi usando un producer e un consumer di esempio scritti in Node.This quickstart will show how to create and connect to an Event Hubs Kafka endpoint using an example producer and consumer written in Node.</p><p>In questo esempio viene utilizzata la libreria [node-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Event Hubs Kafka usando un produttore di esempio e un consumer scritti in python.</p><p>Questo esempio è basato sul [client Apache Kafka Python di Confluent,](https://github.com/confluentinc/confluent-kafka-python)modificato per l'utilizzo con gli hub eventi per Kafka.</p>|
| [invio](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Kafka di Hub eventi usando un produttore e un consumer di esempio scritti in Go.This quickstart will show how to create and connect to an Event Hubs Kafka endpoint using an example producer and consumer written in Go.</p><p>Questo esempio è basato sul [client Apache Kafka Golang di Confluent,](https://github.com/confluentinc/confluent-kafka-go)modificato per l'utilizzo con gli hub eventi per Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Event Hubs Kafka usando un produttore e un consumer di esempio scritti in Go usando la libreria [client Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Questa guida introduttiva mostrerà come creare e connettersi a un endpoint Difka di hub eventi usando l'interfaccia della riga di comando fornita con la distribuzione Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat è un consumatore di linea di comando non-JVM e produttore basato su librdkafka, popolare per la sua velocità e l'ingombro ridotto. Questa guida introduttiva contiene una configurazione di esempio e diversi semplici comandi kafkacat di esempio. | 
 
### <a name="quickstarts-in-docs"></a>Guide introduttive in DOCS
Vedere la guida introduttiva: Streaming di [dati con hub eventi usando il protocollo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) in questo set di contenuti, che fornisce istruzioni dettagliate su come eseguire lo streaming negli hub eventi. Si apprenderà come usare i produttori e gli utenti per comunicare con gli hub eventi con una semplice modifica della configurazione nelle applicazioni. 


## <a name="tutorials"></a>Esercitazioni 

### <a name="tutorials-in-github"></a>Esercitazioni in GitHubTutorials in GitHub
Vedere le esercitazioni seguenti su GitHub:See the following tutorials on GitHub:

| Esercitazione | Descrizione | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Questa esercitazione illustra come connettere flussi Akka a hub eventi abilitati per Kafka senza modificare i client di protocollo o eseguire i propri cluster. Sono disponibili due esercitazioni separate che utilizzano i linguaggi di programmazione Java e **Scala.There** are two separate tutorials using **Java** and Scala programming languages. | 
| [Connettere](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Questo documento illustra l'integrazione di Kafka Connect con gli hub eventi di Azure e la distribuzione dei connettori FileStreamSource e FileStreamSink di base. Sebbene questi connettori non siano destinati all'uso in produzione, illustrano uno scenario Kafka Connect end-to-end in cui Hub eventi di Azure si maschera notendoni come broker Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Questo documento ti guiderà attraverso l'integrazione di Filebeat e Event Hubttotto tramite l'output Kafka di Filebeat. | 
| [Flink (Flink)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Questa esercitazione mostrerà come connettere Apache Flink agli hub eventi abilitati per Kafka senza modificare i client di protocollo o eseguire i propri cluster. | 
| [FluentE](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Questo documento ti guiderà attraverso l'integrazione `out_kafka` di Hub Fluentd e Event utilizzando il plugin di output per Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | In questa esercitazione viene illustrato come scambiare eventi tra consumer e produttori utilizzando protocolli diversi. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Questo tutorial ti guiderà attraverso l'integrazione di Logstash con gli hub di eventi abilitati per Kafka utilizzando i plug-in di input/output Logstash Kafka. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Questa esercitazione mostra come un hub eventi e Kafka MirrorMaker possono integrare una pipeline Kafka esistente in Azure eseguendo il mirroring del flusso di input Kafka nel servizio Hub eventi. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Questa esercitazione mostrerà come connettere Apache NiFi a uno spazio dei nomi Hub eventi. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Le guide introduttive mostrano come creare e connettersi a un endpoint Kafka di Hub eventi utilizzando un produttore e un consumer di esempio scritti nei linguaggi di programmazione Go e Java. |
| [Registro schemi di Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Questa esercitazione illustra come integrare il Registro di sistema dello schema e gli hub eventi per Kafka.This tutorial will walk you through integrating Schema Registry and Event Hubs for Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Questa esercitazione illustra come connettere l'applicazione Spark a un hub eventi senza modificare i client di protocollo o eseguire i cluster Kafka. | 

### <a name="tutorials-in-docs"></a>Esercitazioni in DOCSTutorials in DOCS
Vedere anche l'esercitazione: Elaborare apache Kafka per gli eventi di Hub di eventi usando l'analisi di flusso in questo set di contenuti, che mostra come trasmettere dati in streaming negli hub eventi ed elaborarli con Analisi di flusso di Azure.Also, see the tutorial: [Process Apache Kafka for Event Hubs events using Stream analytics](event-hubs-kafka-stream-analytics.md) in this content set, which shows how to stream data into Event Hubs and process it with Azure Stream Analytics.

## <a name="how-to-guides"></a>Guide pratiche
Vedere le seguenti guide alle procedure nella documentazione:

| Articolo | Descrizione | 
| ------- | ----------- | 
| [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md) | Viene illustrato come eseguire il mirroring di un broker Kafka in un hub eventi utilizzando Kafka MirrorMaker. |
| [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md) | Viene illustrato come connettere l'applicazione Spark agli hub eventi per lo streaming in tempo reale. |
| [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md) | Viene illustrato come connettere Apache Flink a un hub eventi senza modificare i client di protocollo o eseguire i propri cluster. |
| [Integrare Apache Kafka Connect con un hub eventi (anteprima)Integrate Apache Kafka Connect with a event hub (Preview)](event-hubs-kafka-connect-tutorial.md) | Vengono illustrati l'integrazione di Kafka Connect con un hub eventi e la distribuzione dei connettori FileStreamSource e FileStreamSink di base. |
| [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md) | Viene illustrato come connettere Akka Streams a un hub eventi senza modificare i client di protocollo o eseguire i propri cluster. |
| [Usare l'antivita di spring Boot per Apache Kafka con gli hub eventi di AzureUse the Spring Boot Starter for Apache Kafka with Azure Event Hubs](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Viene illustrato come configurare un raccoglitore Spring Cloud Stream basato su Java creato con l'inizializzatore di avvio Spring per l'utilizzo di Apache Kafka con hub eventi di Azure.Demonstrates how to configure a Java-based Spring Cloud Stream Binder created with the Spring Boot Initializer to use Apache Kafka with Azure Event Hubs. |

## <a name="next-steps"></a>Passaggi successivi
Esaminare gli esempi nel GitHub repo [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) nelle cartelle quickstart e tutorials.

