---
title: Introduzione ad Apache Kafka in HDInsight | Documentazione Microsoft
description: "Informazioni su Apache Kafka in HDInsight. Che cos&quot;è, che cosa fa e dove trovare esempi e informazioni introduttive."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 28d213fa23d480635fd4376e22ff54a5e6374350

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Introduzione ad Apache Kafka in HDInsight (anteprima)

[Apache Kafka](https://kafka.apache.org) è una piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. Kafka offre anche una funzionalità di broker di messaggi simile a una coda di messaggi, dove è possibile pubblicare e sottoscrivere flussi dei dati denominati. Kafka in HDInsight fornisce un servizio gestito, a scalabilità e disponibilità elevate nel cloud di Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Perché usare Kafka in HDInsight?

Kafka fornisce le funzionalità seguenti:

* Modelli di messaggistica per la pubblicazione/sottoscrizione: Kafka fornisce un'API Producer per pubblicare record in un argomento Kafka. L'API Consumer viene usata quando si sottoscrive un argomento.

* Elaborazione dei flussi: Kafka viene usato spesso con Apache Storm o Spark per l'elaborazione dei flussi in tempo reale. In Kafka 0.10.0.0 (HDInsight versione 3.5) è stata introdotta un'API di streaming che consente di compilare soluzioni di streaming senza ricorrere a Storm o Spark.

* Scalabilità orizzontale: le partizioni Kafka trasmettono nei nodi del cluster HDInsight. È possibile associare processi consumer a singole partizioni per garantire il bilanciamento del carico durante l'utilizzo dei record.

* Recapito ordinato: in ogni partizione i record vengono archiviati nel flusso nell'ordine in cui sono stato ricevuti. Associando un processo consumer per partizione, è possibile garantire che i record vengano elaborati in ordine.

* Tolleranza di errore: le partizioni possono essere replicate tra i nodi per assicurare la tolleranza di errore.

## <a name="use-cases"></a>Casi d'uso

* **Messaggistica**: poiché supporta il modello di messaggio per la pubblicazione/sottoscrizione, Kafka viene usato spesso come broker di messaggi.

* **Rilevamento delle attività**: poiché Kafka offre la registrazione ordinata dei record, può essere usato per tenere traccia delle attività e per ricrearle. Ad esempio, le azioni dell'utente in un sito Web o in un'applicazione.

* **Aggregazione**: usando l'elaborazione dei flussi, è possibile aggregare informazioni da flussi diversi per combinare e centralizzare le informazioni in dati operativi.

* **Trasformazione**: usando l'elaborazione dei flussi, è possibile combinare e arricchire dati da più argomenti di input in uno o più argomenti di output.

## <a name="where-do-i-start"></a>Dove iniziare?

Vedere [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight) per i passaggi necessari per creare un cluster Kafka e per usare Kafka, inclusi esempi basati su Java dell'uso dell'API di produzione, utilizzo e streaming.

## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per informazioni su come usare Apache Kafka in HDInsight:

* [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight)

* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Usare Apache Storm (anteprima) con Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)




<!--HONumber=Nov16_HO3-->


