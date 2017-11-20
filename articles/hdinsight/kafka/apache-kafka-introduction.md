---
title: 'Introduzione ad Apache Kafka in HDInsight: Azure | Microsoft Docs'
description: "Informazioni su Apache Kafka in HDInsight: che cos'è, quale funzione svolge e dove trovare esempi e informazioni introduttive."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: c4e0d792ae8f4c17d53430f49d81d179e56b9722
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Introduzione ad Apache Kafka in HDInsight

[Apache Kafka](https://kafka.apache.org) è una piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. Kafka offre anche una funzionalità di broker di messaggi simile a una coda di messaggi, dove è possibile pubblicare e sottoscrivere flussi dei dati denominati. Kafka in HDInsight fornisce un servizio gestito, a scalabilità e disponibilità elevate nel cloud di Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Perché usare Kafka in HDInsight?

Kafka in HDInsight offre le funzionalità seguenti:

* Contratto di servizio: [Informazioni sul contratto di servizio per HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Modelli di messaggistica per la pubblicazione/sottoscrizione: Kafka fornisce un'API Producer per pubblicare record in un argomento Kafka. L'API Consumer viene usata quando si sottoscrive un argomento.

* Elaborazione dei flussi: Kafka viene usato spesso con Apache Storm o Spark per l'elaborazione dei flussi in tempo reale. In Kafka 0.10.0.0 (HDInsight versione 3.5 e 3.6) è stata introdotta un'API di streaming che consente di compilare soluzioni di streaming senza ricorrere a Storm o Spark.

* Scalabilità orizzontale: le partizioni Kafka trasmettono nei nodi del cluster HDInsight. È possibile associare processi consumer a singole partizioni per garantire il bilanciamento del carico durante l'utilizzo dei record.

* Recapito ordinato: in ogni partizione i record vengono archiviati nel flusso nell'ordine in cui sono stato ricevuti. Associando un processo consumer per partizione, è possibile garantire che i record vengano elaborati in ordine.

* Tolleranza di errore: le partizioni possono essere replicate tra i nodi per assicurare la tolleranza di errore.

* Integrazione con Azure Managed Disks: il servizio Managed Disks offre scalabilità e velocità effettiva maggiori per i dischi usati dalle macchine virtuali nel cluster HDInsight.

    Il servizio Managed Disks è abilitato per impostazione predefinita per Kafka in HDInsight. Il numero di dischi usati per ogni nodo può essere configurato durante la creazione di HDInsight. Per altre informazioni sui dischi gestiti, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

    Per informazioni sulla configurazione di dischi gestiti con Kafka in HDInsight, vedere [Configurare l'archiviazione e la scalabilità per Apache Kafka in HDInsight](apache-kafka-scalability.md).

## <a name="use-cases"></a>Casi d'uso

* **Messaggistica**: poiché supporta il modello di messaggio per la pubblicazione/sottoscrizione, Kafka viene usato spesso come broker di messaggi.

* **Rilevamento delle attività**: poiché Kafka offre la registrazione ordinata dei record, può essere usato per tenere traccia delle attività e per ricrearle. Ad esempio, le azioni dell'utente in un sito Web o in un'applicazione.

* **Aggregazione**: usando l'elaborazione dei flussi, è possibile aggregare informazioni da flussi diversi per combinare e centralizzare le informazioni in dati operativi.

* **Trasformazione**: usando l'elaborazione dei flussi, è possibile combinare e arricchire dati da più argomenti di input in uno o più argomenti di output.

## <a name="architecture"></a>Architettura

![Configurazione del cluster Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

Questo diagramma mostra una configurazione tipica di Kafka che usa gruppi di consumer, partizionamento e replica per offrire la lettura parallela degli eventi con tolleranza di errore. Apache ZooKeeper è pensato per transazioni simultanee, resilienti e a bassa latenza, perché gestisce lo stato del cluster Kafka. Kafka archivia i record in *argomenti*. I record vengono prodotti da *producer* e usati da *consumer*. I producer recuperano i record da *broker* di Kafka. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka. Viene creata una partizione per ogni consumer, per consentire l'elaborazione parallela dei dati di streaming. Viene usata la replica per distribuire le partizioni tra i nodi e garantire protezione in caso di interruzioni dei nodi (broker). Una partizione indicata da *(L)* rappresenta l'elemento leader per la partizione specifica. Il traffico dei producer viene indirizzato al leader di ogni nodo, usando lo stato gestito da ZooKeeper.

> [!IMPORTANT]
> Kafka non è a conoscenza dell'hardware sottostante (rack) nel data center di Azure. Per garantire che le partizioni siano bilanciate correttamente nell'hardware sottostante, vedere [Configurare la disponibilità elevata dei dati (Kafka)](apache-kafka-high-availability.md).

## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per informazioni su come usare Apache Kafka in HDInsight:

* [Get started with Kafka on HDInsight](apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight)

* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](apache-kafka-mirroring.md)

* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)