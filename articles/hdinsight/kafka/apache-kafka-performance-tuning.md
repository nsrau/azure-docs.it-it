---
title: Ottimizzazione delle prestazioni per i cluster HDInsight di Apache Kafka
description: Fornisce una panoramica delle tecniche per l'ottimizzazione di carichi di lavoro di Apache Kafka in HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689346"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Ottimizzazione delle prestazioni per i cluster HDInsight di Apache Kafka

Questo articolo offre alcuni suggerimenti per ottimizzare le prestazioni dei carichi di lavoro di Apache Kafka in HDInsight. Lo stato attivo è sulla modifica di producer e la configurazione di Service broker. Esistono diversi modi di misurazione delle prestazioni e le ottimizzazioni che si applicano varia in base a esigenze aziendali.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Gli argomenti Kafka vengono usati per organizzare i record. I record vengono prodotte dai produttori e consumati dai consumatori. I producer invia i record ai Broker Kafka, che quindi archiviare i dati. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Record di partizione degli argomenti tra broker. Quando si usano i record, è possibile usare fino a un consumer per partizione, per ottenere l'elaborazione parallela dei dati.

La replica viene utilizzata per duplicare le partizioni tra i nodi. Ciò consente di evitare interruzioni del nodo (broker). Una singola partizione all'interno del gruppo di repliche viene designata come leader partizione. Il traffico dei producer viene indirizzato al leader di ogni nodo, usando lo stato gestito da ZooKeeper.

## <a name="identify-your-scenario"></a>Identificare lo scenario

Le prestazioni di Apache Kafka presenta due aspetti principali: velocità effettiva e latenza. Velocità effettiva è la velocità massima in corrispondenza del quale i dati possono essere elaborati. Una maggiore velocità effettiva in genere è preferibile. La latenza è il tempo che necessario per i dati da memorizzare o recuperata. Ridurre la latenza in genere è preferibile. Può essere difficile trovare il giusto equilibrio tra velocità effettiva, latenza e il costo dell'infrastruttura dell'applicazione. I requisiti di prestazioni corrisponderà probabilmente uno dei tre comuni situazioni seguenti, in base se è necessaria una velocità effettiva elevata, bassa latenza o entrambi:

* Velocità effettiva elevata, bassa latenza. Questo scenario richiede sia una velocità effettiva elevata e bassa latenza (~ 100 millisecondi). È un esempio di questo tipo di applicazione del servizio di monitoraggio della disponibilità.
* Velocità effettiva elevata, una latenza elevata. Questo scenario richiede una velocità effettiva elevata (~1.5 GBps), ma può tollerare la latenza più elevata (< 250 ms). Un esempio di questo tipo di applicazione è l'inserimento dei dati di telemetria per quasi in tempo reale dei processi, ad esempio le applicazioni di rilevamento delle intrusioni e sicurezza.
* Bassa velocità effettiva, bassa latenza. Questo scenario richiede a bassa latenza (< 10 ms) per l'elaborazione in tempo reale, ma può tollerare la velocità effettiva inferiore. Un esempio di questo tipo di applicazione è controlli ortografia e grammatica online.

## <a name="producer-configurations"></a>Configurazioni di Producer

Le sezioni seguenti verranno illustrate alcune delle proprietà di configurazione più importanti per ottimizzare le prestazioni dei producer Kafka. Per una spiegazione dettagliata di tutte le proprietà di configurazione, vedere [documentazione di Apache Kafka in configurazioni producer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Dimensioni dei batch

I producer di Apache Kafka assemblare i gruppi di messaggi (detto batch) che vengono inviati come un'unità da archiviare in una partizione di archiviazione singolo. Le dimensioni del batch indica il numero di byte che deve essere presente prima della trasmissione di tale gruppo. Aumentando il `batch.size` parametro può aumentare la velocità effettiva, poiché riduce il sovraccarico dalla rete e le richieste dei / o l'elaborazione. Sotto carico ridotto, le dimensioni del batch maggiore possono aumentare la latenza media invio Kafka nel producer è in attesa di un batch sia pronto. Con un carico pesante, è consigliabile per aumentare le dimensioni di batch per migliorare la velocità effettiva e latenza.

### <a name="producer-required-acknowledgements"></a>Riconoscimenti necessari producer

Il producer necessario `acks` configurazione determina il numero di riconoscimenti necessari dal leader partizione prima che una richiesta di scrittura venga considerata completata. Questa impostazione influisce su affidabilità dei dati e richiede i valori della `0`, `1`, o `-1`. Il valore di `-1` significa che un riconoscimento deve essere ricevuto da tutte le repliche prima che venga completata l'operazione di scrittura. Impostazione `acks = -1` offre maggiori garanzie contro la perdita di dati, ma anche i risultati in una latenza maggiore e velocità effettiva inferiore. Se i requisiti dell'applicazione richiedono una velocità effettiva, provare a impostare `acks = 0` o `acks = 1`. Tenere presente, che non riconoscono le tutte le repliche può ridurre l'affidabilità dei dati.

### <a name="compression"></a>Compressione

Un producer Kafka può essere configurato per comprimere i messaggi prima di inviarli ai Broker. Il `compression.type` impostazione specifica il codec di compressione da usare. I codec di compressione supportati sono "gzip", "snappy," e "lz4." La compressione è utile e da considerare se è presente una limitazione per capacità disco.

Tra i due codec di compressione comunemente utilizzati, `gzip` e `snappy`, `gzip` dispone di un rapporto di compressione maggiore, con conseguente utilizzo disco inferiore al costo di un carico della CPU maggiore. Il `snappy` codec offre meno compressione con un minor overhead di CPU. È possibile decidere quale codec da usare in base alle limitazioni di CPU del disco o il producer di Service broker. `gzip` possibile comprimere i dati a una tariffa cinque volte maggiore `snappy`.

Utilizzando la compressione dei dati aumenterà il numero di record che possono essere archiviati in un disco. Anche possibile aumentare sovraccarico della CPU nei casi in cui si verifica una mancata corrispondenza tra i formati di compressione utilizzati per il producer e il broker. come i dati devono essere compressi prima di inviare e poi decompressi prima dell'elaborazione.

## <a name="broker-settings"></a>Impostazioni di Service Broker

Le sezioni seguenti verranno illustrate alcune delle impostazioni più importanti per ottimizzare le prestazioni dei broker di Kafka. Per una spiegazione dettagliata di tutte le impostazioni di broker, vedere [documentazione di Apache Kafka in configurazioni producer](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Numero di dischi

I dischi di archiviazione non dispongono di IOPS (Input/Output operazioni al secondo) e byte al secondo in lettura/scrittura. Quando si creano nuove partizioni, Kafka archivia ogni nuova partizione nel disco con minor numero di partizioni esistenti per bilanciarli tra i dischi disponibili. Nonostante strategia di archiviazione, durante l'elaborazione di centinaia di repliche di partizione in ogni disco, Kafka possa saturare facilmente la velocità effettiva disponibile sul disco. In questo caso il compromesso è tra velocità effettiva e i costi. Se l'applicazione richiede velocità effettiva maggiore, creare un cluster con più managed disks per Service broker. HDInsight non supporta attualmente l'aggiunta di dischi gestiti per un cluster in esecuzione. Per altre informazioni su come configurare il numero di dischi gestiti, vedere [configurare l'archiviazione e scalabilità per Apache Kafka in HDInsight](apache-kafka-scalability.md). Comprendere le implicazioni di costi di aumentare lo spazio di archiviazione per i nodi nel cluster.

### <a name="number-of-topics-and-partitions"></a>Numero di argomenti e partizioni

I producer Kafka di scrittura agli argomenti. I consumer Kafka leggono dagli argomenti. Un argomento è associato un log che è una struttura di dati su disco. Kafka aggiunge i record da un producer(s) alla fine di un log di argomento. Un log di argomento è costituito da un numero di partizioni distribuite in più file. Questi file vengono, a sua volta, distribuiti in più nodi del cluster Kafka. Consumer di leggere gli argomenti Kafka loro cadenza e scegliere la posizione (offset) nel Registro di argomento.

Ogni partizione Kafka è un file di log nel sistema e thread producer può scrivere contemporaneamente in più log. Analogamente, poiché ogni thread consumer legge i messaggi da una partizione, utilizzato da più partizioni viene gestita in parallelo anche.

Aumentando la densità di partizione (il numero di partizioni per Service broker) aggiunge un sovraccarico correlato a operazioni sui metadati e per ogni partizione richiesta/risposta tra il leader di partizione e relativo FOLLOWER. Anche in assenza di dati che attraversa, le repliche di partizione comunque recupero dati dal leader, che comporta ulteriori operazioni di elaborazione per inviare e ricevere richieste attraverso la rete.

Per Apache Kafka cluster 1.1 e versioni successive in HDInsight, è consigliabile avere un massimo di 1000 partizioni per Service broker, incluse le repliche. L'aumento del numero di partizioni per broker riduce la velocità effettiva e può anche causa l'indisponibilità di argomento. Per altre informazioni sul supporto delle partizioni Kafka, vedere [il post di blog ufficiale di Apache Kafka sull'aumento del numero delle partizioni supportate nella versione 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Per informazioni dettagliate su come modificare gli argomenti, vedere [Apache Kafka: modifica di argomenti](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Numero di repliche

Fattore di replica maggiore comporta ulteriori richieste tra i leader di partizione e di FOLLOWER. Di conseguenza, un fattore di replica maggiore utilizza più dischi e della CPU per gestire le richieste aggiuntive, aumentando scrivere latenza e la riduzione della velocità effettiva.

È consigliabile usare almeno replica 3x per Kafka in HDInsight di Azure. Aree di Azure più avere tre domini di errore, ma in aree con solo due domini di errore, gli utenti devono usare la replica di 4 x.

Per ulteriori informazioni sulla replica, vedere [Apache Kafka: replica](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: aumento fattore di replica](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Passaggi successivi

* [Elaborazione di miliardi di eventi al giorno con Apache Kafka in Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
