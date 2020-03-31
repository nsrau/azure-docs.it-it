---
title: Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight
description: Fornisce una panoramica delle tecniche per l'ottimizzazione dei carichi di lavoro apache Kafka in Azure HDInsight.Provides an overview of techniques for optimizing Apache Kafka workloads on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494917"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight

Questo articolo fornisce alcuni suggerimenti per ottimizzare le prestazioni dei carichi di lavoro Apache Kafka in HDInsight.This article gives some suggestions for optimizing the performance of your Apache Kafka workloads in HDInsight. L'obiettivo è quello di regolare la configurazione di produttori e broker. Esistono diversi modi per misurare le prestazioni e le ottimizzazioni applicate dipendono dalle esigenze aziendali.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Gli argomenti Kafka vengono utilizzati per organizzare i record. I record vengono prodotti da producer e usati da consumer. I produttori inviano record ai broker Kafka, che poi memorizzano i dati. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Record di partizione degli argomenti tra broker. Quando si usano i record, è possibile usare fino a un consumer per partizione, per ottenere l'elaborazione parallela dei dati.

La replica viene utilizzata per duplicare le partizioni tra i nodi. Questo protegge contro le interruzioni del nodo (broker). Una singola partizione tra il gruppo di repliche viene designata come leader della partizione. Il traffico dei producer viene indirizzato al leader di ogni nodo, usando lo stato gestito da ZooKeeper.

## <a name="identify-your-scenario"></a>Identificare lo scenario

Le prestazioni di Apache Kafka hanno due aspetti principali: velocità effettiva e latenza. La velocità effettiva è la velocità massima con cui è possibile elaborare i dati. Una maggiore velocità effettiva è in genere migliore. La latenza è il tempo necessario per l'archiviazione o il recupero dei dati. Una latenza inferiore è in genere migliore. Trovare il giusto equilibrio tra velocità effettiva, latenza e costo dell'infrastruttura dell'applicazione può essere difficile. I requisiti di prestazioni corrisponderanno probabilmente a una delle tre situazioni comuni seguenti, a seconda che siano necessarie velocità effettiva elevata, bassa latenza o entrambe:

* Alta velocità effettiva, bassa latenza. Questo scenario richiede sia una velocità effettiva elevata che una bassa latenza (100 millisecondi). Un esempio di questo tipo di applicazione è il monitoraggio della disponibilità del servizio.
* Alta velocità effettiva, alta latenza. Questo scenario richiede una velocità effettiva elevata (1,5 GBps) ma può tollerare una latenza maggiore (< 250 ms). Un esempio di questo tipo di applicazione è l'inserimento di dati di telemetria per processi quasi in tempo reale come le applicazioni di sicurezza e rilevamento delle intrusioni.
* Bassa velocità effettiva, bassa latenza. Questo scenario richiede bassa latenza (< 10 ms) per l'elaborazione in tempo reale, ma può tollerare una velocità effettiva inferiore. Un esempio di questo tipo di applicazione è il controllo ortografico e grammaticale online.

## <a name="producer-configurations"></a>Configurazioni produttore

Le sezioni seguenti evidenziano alcune delle proprietà di configurazione più importanti per ottimizzare le prestazioni dei produttori Kafka. Per una spiegazione dettagliata di tutte le proprietà di configurazione, vedere la documentazione di [Apache Kafka sulle configurazioni dei produttori](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Dimensioni dei batch

I produttori Apache Kafka assemblano gruppi di messaggi (chiamati batch) che vengono inviati come unità da memorizzare in un'unica partizione di archiviazione. Dimensione batch indica il numero di byte che devono essere presenti prima che tale gruppo venga trasmesso. L'aumento del parametro può aumentare la velocità effettiva, perché riduce l'overhead di elaborazione dalle richieste di rete e I/O.Increasing the `batch.size` parameter can increase throughput, because it reduces the processing overhead from network and IO requests. Sotto carico leggero, l'aumento delle dimensioni del batch può aumentare la latenza di invio Kafka mentre il produttore attende che un batch sia pronto. In condizioni di carico elevato, è consigliabile aumentare le dimensioni del batch per migliorare la velocità effettiva e la latenza.

### <a name="producer-required-acknowledgments"></a>Riconoscimenti richiesti dal produttore

La configurazione `acks` richiesta del producer determina il numero di riconoscimenti richiesti dal leader della partizione prima che una richiesta di scrittura venga considerata completata. Questa impostazione influisce sull'affidabilità `1`dei `-1`dati e accetta valori di `0`, , o . Il valore `-1` di indica che un riconoscimento deve essere ricevuto da tutte le repliche prima del completamento della scrittura. L'impostazione `acks = -1` garantisce una maggiore garanzia contro la perdita di dati, ma comporta anche una latenza più elevata e una velocità effettiva inferiore. Se i requisiti dell'applicazione richiedono `acks = 0` `acks = 1`una velocità effettiva maggiore, provare a impostare o . Tenere presente che la non riconoscimento di tutte le repliche può ridurre l'affidabilità dei dati.

### <a name="compression"></a>Compressione

Un produttore Kafka può essere configurato per comprimere i messaggi prima di inviarli ai broker. L'impostazione `compression.type` specifica il codec di compressione da utilizzare. I codec di compressione supportati sono "gzip", "snappy" e "lz4". La compressione è utile e deve essere considerata se esiste una limitazione della capacità del disco.

Tra i due codec `gzip` di `snappy` `gzip` compressione comunemente utilizzati, e , ha un rapporto di compressione più elevato, che si traduce in un minore utilizzo del disco al costo di un carico della CPU più elevato. Il `snappy` codec fornisce meno compressione con meno sovraccarico della CPU. È possibile decidere quale codec utilizzare in base alle limitazioni della CPU del disco broker o del produttore. `gzip`possibile comprimere i dati a `snappy`una velocità cinque volte superiore a .

L'utilizzo della compressione dei dati aumenterà il numero di record che possono essere archiviati su un disco. Può anche aumentare l'overhead della CPU nei casi in cui c'è una mancata corrispondenza tra i formati di compressione utilizzati dal produttore e il broker. come i dati devono essere compressi prima dell'invio e quindi decompressi prima dell'elaborazione.

## <a name="broker-settings"></a>Impostazioni broker

Le seguenti sezioni evidenzieranno alcune delle impostazioni più importanti per ottimizzare le prestazioni dei tuoi broker Kafka. Per una spiegazione dettagliata di tutte le impostazioni del broker, vedere la documentazione di [Apache Kafka sulle configurazioni dei produttori](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Numero di dischi

I dischi di archiviazione hanno operazioni di I/O al secondo limitate (operazioni di input/output al secondo) e byte di lettura/scrittura al secondo. Quando si creano nuove partizioni, Kafka archivia ogni nuova partizione sul disco con il minor numero di partizioni esistenti per bilanciarle tra i dischi disponibili. Nonostante la strategia di archiviazione, durante l'elaborazione di centinaia di repliche di partizioni su ogni disco, Kafka può saturare facilmente la velocità effettiva disponibile del disco. Il compromesso qui è tra velocità effettiva e costo. Se l'applicazione richiede una velocità effettiva maggiore, creare un cluster con più dischi gestiti per broker. HDInsight attualmente non supporta l'aggiunta di dischi gestiti a un cluster in esecuzione. Per ulteriori informazioni su come configurare il numero di dischi gestiti, vedere [Configurare l'archiviazione e la scalabilità per Apache Kafka in HDInsight.](apache-kafka-scalability.md) Comprendere le implicazioni in termini di costi derivanti dall'aumento dello spazio di archiviazione per i nodi del cluster.

### <a name="number-of-topics-and-partitions"></a>Numero di argomenti e partizioni

I produttori Kafka scrivono agli argomenti. I consumatori Kafka leggono da argomenti. Un argomento è associato a un log, ovvero una struttura di dati su disco. Kafka aggiunge i record da un producer (s) alla fine di un log dell'argomento. Un log degli argomenti è costituito da molte partizioni distribuite su più file. Questi file sono, a loro volta, distribuiti su più nodi del cluster Kafka. I consumatori leggono dagli argomenti di Kafka alla loro cadenza e possono scegliere la loro posizione (offset) nel registro degli argomenti.

Ogni partizione Kafka è un file di log nel sistema e i thread producer possono scrivere in più log contemporaneamente. Analogamente, poiché ogni thread consumer legge i messaggi da una partizione, anche l'utilizzo da più partizioni viene gestito in parallelo.

L'aumento della densità delle partizioni (il numero di partizioni per broker) aggiunge un overhead correlato alle operazioni sui metadati e alla richiesta/risposta per partizione tra il responsabile della partizione e i relativi follower. Anche in assenza di flusso di dati, le repliche delle partizioni recuperano ancora i dati dai leader, il che comporta un'elaborazione aggiuntiva per le richieste di invio e ricezione in rete.

Per i cluster Apache Kafka 1.1 e versioni successive in HDInsight, è consigliabile avere un massimo di 1000 partizioni per broker, incluse le repliche. L'aumento del numero di partizioni per broker riduce la velocità effettiva e può anche causare indisponibilità degli argomenti. Per ulteriori informazioni sul supporto delle partizioni Kafka, vedere il post di blog ufficiale di [Apache Kafka sull'aumento del numero di partizioni supportate nella versione 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Per informazioni dettagliate sulla modifica degli argomenti, vedere [Apache Kafka: modifica degli argomenti](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Numero di repliche

Un fattore di replica più elevato comporta richieste aggiuntive tra il responsabile della partizione e i follower. Di conseguenza, un fattore di replica più elevato consuma più disco e CPU per gestire richieste aggiuntive, aumentando la latenza di scrittura e riducendo la velocità effettiva.

È consigliabile usare almeno la replica 3volte per Kafka in Azure HDInsight.We recommend that you use at least 3x replication for Kafka in Azure HDInsight. La maggior parte delle aree di Azure ha tre domini di errore, ma nelle aree con solo due domini di errore, gli utenti devono usare la replica 4x.

Per ulteriori informazioni sulla replica, vedere [Apache Kafka: replica](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: fattore](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)di replica crescente .

## <a name="next-steps"></a>Passaggi successivi

* [Elaborazione di miliardi di eventi al giorno con Apache Kafka in Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
