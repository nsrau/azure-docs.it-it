---
title: Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight
description: Viene fornita una panoramica delle tecniche per ottimizzare i carichi di lavoro Apache Kafka in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75494917"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight

Questo articolo offre alcuni suggerimenti per l'ottimizzazione delle prestazioni dei carichi di lavoro Apache Kafka in HDInsight. L'obiettivo è quello di modificare la configurazione di Producer e broker. Esistono diversi modi per misurare le prestazioni e le ottimizzazioni applicate variano in base alle esigenze aziendali.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Gli argomenti Kafka vengono usati per organizzare i record. I record vengono prodotti da producer e usati da consumer. I produttori inviano i record ai broker Kafka, che quindi archiviano i dati. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Record di partizione degli argomenti tra broker. Quando si usano i record, è possibile usare fino a un consumer per partizione, per ottenere l'elaborazione parallela dei dati.

La replica viene utilizzata per duplicare le partizioni tra i nodi. Questa operazione protegge da interruzioni del nodo (Broker). Una singola partizione tra il gruppo di repliche viene designata come leader della partizione. Il traffico dei producer viene indirizzato al leader di ogni nodo, usando lo stato gestito da ZooKeeper.

## <a name="identify-your-scenario"></a>Identificare lo scenario

Apache Kafka prestazioni hanno due aspetti principali: velocità effettiva e latenza. La velocità effettiva è la velocità massima con cui è possibile elaborare i dati. Una velocità effettiva più elevata è in genere migliore. La latenza è il tempo necessario per archiviare o recuperare i dati. Una latenza più bassa è in genere migliore. Trovare il giusto equilibrio tra velocità effettiva, latenza e costo dell'infrastruttura dell'applicazione può risultare complesso. È probabile che i requisiti di prestazioni corrispondano a una delle tre situazioni comuni seguenti, a seconda che sia necessaria una velocità effettiva elevata, bassa latenza o entrambi:

* Velocità effettiva elevata, bassa latenza. Questo scenario richiede una velocità effettiva elevata e bassa latenza (~ 100 millisecondi). Un esempio di questo tipo di applicazione è il monitoraggio della disponibilità del servizio.
* Velocità effettiva elevata, latenza elevata. Questo scenario richiede una velocità effettiva elevata (~ 1,5 GBps) ma può tollerare una latenza più elevata (< 250 ms). Un esempio di questo tipo di applicazione è l'inserimento di dati di telemetria per processi quasi in tempo reale come le applicazioni di rilevamento di sicurezza e intrusioni.
* Bassa velocità effettiva, bassa latenza. Questo scenario richiede una bassa latenza (< 10 ms) per l'elaborazione in tempo reale, ma può tollerare una velocità effettiva inferiore. Un esempio di questo tipo di applicazione è la verifica della grammatica e dell'ortografia online.

## <a name="producer-configurations"></a>Configurazioni Producer

Le sezioni seguenti evidenziano alcune delle proprietà di configurazione più importanti per ottimizzare le prestazioni dei producer Kafka. Per una spiegazione dettagliata di tutte le proprietà di configurazione, vedere [Apache Kafka documentazione sulle configurazioni del produttore](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Dimensioni dei batch

Apache Kafka produttori assemblano gruppi di messaggi, detti batch, che vengono inviati come unità da archiviare in una singola partizione di archiviazione. Dimensioni batch indica il numero di byte che devono essere presenti prima che il gruppo venga trasmesso. L'aumento del `batch.size` parametro può aumentare la velocità effettiva, perché riduce l'overhead di elaborazione dalle richieste di rete e di i/o. Con il caricamento leggero, le dimensioni del batch aumentate potrebbero aumentare la latenza di invio Kafka quando il produttore attende che un batch sia pronto. In condizioni di carico elevato, è consigliabile aumentare le dimensioni del batch per migliorare la velocità effettiva e la latenza.

### <a name="producer-required-acknowledgments"></a>Riconoscimenti richiesti dal produttore

La configurazione richiesta dal Producer `acks` determina il numero di riconoscimenti richiesti dal responsabile della partizione prima che una richiesta di scrittura venga considerata completata. Questa impostazione influiscono sull'affidabilità dei dati e accetta i valori di `0` , `1` o `-1` . Il valore di `-1` indica che un riconoscimento deve essere ricevuto da tutte le repliche prima del completamento della scrittura. L'impostazione garantisce una `acks = -1` maggiore protezione dalla perdita di dati, ma comporta anche una latenza più elevata e una velocità effettiva inferiore. Se i requisiti dell'applicazione richiedono una velocità effettiva superiore, provare a impostare `acks = 0` o `acks = 1` . Tenere presente che il mancato riconoscimento di tutte le repliche può ridurre l'affidabilità dei dati.

### <a name="compression"></a>Compressione

Un Producer Kafka può essere configurato per comprimere i messaggi prima di inviarli ai broker. L' `compression.type` impostazione specifica il codec di compressione da usare. I codec di compressione supportati sono "gzip", "snapper" e "LZ4". La compressione è vantaggiosa e deve essere considerata se esiste una limitazione sulla capacità del disco.

Tra i due codec di compressione di uso comune, `gzip` e `snappy` , `gzip` ha un rapporto di compressione più elevato, che comporta un minore utilizzo del disco al costo di un carico superiore della CPU. Il `snappy` codec offre una minore compressione con minore sovraccarico della CPU. È possibile decidere quale codec utilizzare in base alle limitazioni del disco broker o della CPU del Producer. `gzip` consente di comprimere i dati a una velocità cinque volte superiore a `snappy` .

L'utilizzo della compressione dei dati aumenterà il numero di record che possono essere archiviati su un disco. Può anche aumentare il sovraccarico della CPU nei casi in cui non vi sia una corrispondenza tra i formati di compressione usati dal Producer e dal broker. Poiché i dati devono essere compressi prima dell'invio e quindi decompressi prima dell'elaborazione.

## <a name="broker-settings"></a>Impostazioni broker

Le sezioni seguenti evidenziano alcune delle impostazioni più importanti per ottimizzare le prestazioni dei broker di Kafka. Per una spiegazione dettagliata di tutte le impostazioni di Service Broker, vedere [Apache Kafka documentazione sulle configurazioni del produttore](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Numero di dischi

I dischi di archiviazione hanno IOPS limitati (operazioni di input/output al secondo) e byte di lettura/scrittura al secondo. Quando si creano nuove partizioni, Kafka archivia ogni nuova partizione sul disco con minor numero di partizioni esistenti per bilanciarle tra i dischi disponibili. Nonostante la strategia di archiviazione, quando si elaborano centinaia di repliche di partizioni in ogni disco, Kafka può saturare facilmente la velocità effettiva del disco disponibile. Il compromesso è tra la velocità effettiva e i costi. Se l'applicazione richiede una maggiore velocità effettiva, creare un cluster con più dischi gestiti per ogni broker. HDInsight attualmente non supporta l'aggiunta di dischi gestiti a un cluster in esecuzione. Per altre informazioni su come configurare il numero di dischi gestiti, vedere [configurare l'archiviazione e la scalabilità per Apache Kafka in HDInsight](apache-kafka-scalability.md). Comprendere le implicazioni dei costi dell'aumento dello spazio di archiviazione per i nodi del cluster.

### <a name="number-of-topics-and-partitions"></a>Numero di argomenti e partizioni

I produttori Kafka scrivono negli argomenti. I consumer Kafka leggono dagli argomenti. Un argomento è associato a un log, che è una struttura di dati su disco. Kafka aggiunge i record di uno o più Producer alla fine di un log degli argomenti. Un log degli argomenti è costituito da molte partizioni distribuite su più file. Questi file sono, a loro volta, distribuiti in più nodi del cluster Kafka. I consumer leggono gli argomenti Kafka a cadenza e possono scegliere la posizione (offset) nel log degli argomenti.

Ogni partizione Kafka è un file di log nel sistema e i thread producer possono scrivere in più log simultaneamente. Analogamente, poiché ogni thread consumer legge i messaggi da una partizione, anche l'utilizzo di più partizioni viene gestito in parallelo.

Aumentando la densità della partizione (il numero di partizioni per Broker) viene aggiunto un overhead correlato alle operazioni sui metadati e alla richiesta/risposta per partizione tra il leader della partizione e i relativi follower. Anche in assenza di flussi di dati, le repliche di partizione continuano a recuperare i dati dai responsabili, il che comporta un'elaborazione aggiuntiva per le richieste di invio e ricezione sulla rete.

Per i cluster Apache Kafka 1,1 e versioni successive in HDInsight, è consigliabile disporre di un massimo di 1000 partizioni per broker, incluse le repliche. L'aumento del numero di partizioni per broker riduce la velocità effettiva e può anche causare la mancata disponibilità degli argomenti. Per altre informazioni sul supporto delle partizioni Kafka, vedere [il post di Blog ufficiale Apache Kafka sull'aumento del numero di partizioni supportate nella versione 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Per informazioni dettagliate sulla modifica degli argomenti, vedere [Apache Kafka: modifica di argomenti](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Numero di repliche

Il fattore di replica più elevato comporta richieste aggiuntive tra il leader della partizione e i follower. Di conseguenza, un fattore di replica più elevato utilizza più dischi e CPU per gestire richieste aggiuntive, aumentando la latenza di scrittura e diminuendo la velocità effettiva.

Si consiglia di usare almeno la replica 3x per Kafka in Azure HDInsight. La maggior parte delle aree di Azure ha tre domini di errore, ma in aree con solo due domini di errore, gli utenti devono usare la replica 4x.

Per ulteriori informazioni sulla replica, vedere [Apache Kafka: replica](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: incremento del fattore di replica](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Passaggi successivi

* [Elaborazione di miliardi di eventi al giorno con Apache Kafka in Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
