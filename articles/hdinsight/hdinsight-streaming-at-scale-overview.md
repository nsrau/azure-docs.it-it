---
title: Flussi scalabili in Azure HDInsight | Microsoft Docs
description: Come usare i flussi di dati con cluster HDInsight scalabili.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 46b5723805ab5d8bc1cf5b5183d9501cd3e4e3a2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streaming su larga scala in HDInsight

Le soluzioni per Big Data in tempo reale agiscono su dati in movimento. In genere, questi dati hanno il maggior valore al momento dell'arrivo. Se il flusso di dati in ingresso diventa più grande delle dimensioni gestibili in quel momento, potrebbe risultare necessario limitare le risorse. Un cluster HDInsight offre in alternativa funzioni di scalabilità verticale, consentendo di soddisfare le esigenze della soluzione con l'aggiunta di nodi su richiesta.

In un'applicazione per l'uso dei flussi, una o più origini dati generano eventi (talvolta milioni al secondo) che devono essere elaborati rapidamente senza eliminare le informazioni utili. Gli eventi in entrata vengono gestiti tramite la *memorizzazione nel buffer del flusso*, tecnica nota anche come *accodamento degli eventi*, eseguita da un servizio come [Kafka](kafka/apache-kafka-introduction.md) o [Hub eventi](https://azure.microsoft.com/services/event-hubs/). Dopo aver raccolto gli eventi, è quindi possibile analizzare i dati tramite un sistema di analisi in tempo reale all'interno del livello di *elaborazione dei flussi*, come [Storm](storm/apache-storm-overview.md) o [Spark Streaming](spark/apache-spark-streaming-overview.md). I dati elaborati possono essere archiviati in sistemi di archiviazione a lungo termine, come [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) e visualizzati in tempo reale in un dashboard di business intelligence, come [Power BI](https://powerbi.microsoft.com), Tableau o una pagina Web personalizzata.

![Modelli di flusso di HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka offre un servizio di accodamento dei messaggi con velocità effettiva elevata e bassa latenza e fa ora parte della famiglia di prodotti software open source di Apache. Kafka usa un modello di messaggistica basato su pubblicazione e sottoscrizione e archivia i flussi di dati partizionati in modo sicuro in un cluster distribuito con replica. Kafka supporta la scalabilità lineare man mano che aumenta la velocità effettiva.

Per altre informazioni, vedere [Introduzione ad Apache Kafka in HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm è un sistema di calcolo distribuito, a tolleranza di errore e open source ottimizzato per l'elaborazione di flussi di dati in tempo reale con Hadoop. L'unità principale dei dati per un evento in ingresso è una tupla, ovvero un set non modificabile di coppie chiave/valore. Una sequenza illimitata di queste tuple forma un flusso, originato da uno spout. Lo spout esegue il wrapping di un'origine dati di flussi (ad esempio Kafka) e genera le tuple. Una topologia Storm è una sequenza di trasformazioni su questi flussi.

Per altre informazioni, vedere [Informazioni su Apache Storm in Azure HDInsight](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming è un'estensione di Spark, che consente di riutilizzare lo stesso codice usato per l'elaborazione batch. È possibile combinare sia query in batch che interattive nella stessa applicazione. A differenza di Storm, Spark Streaming supporta una semantica di elaborazione con stato di tipo exactly-once. Con l'uso in combinazione con l'[API Direct Kafka](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), che assicura che tutti i dati Kafka vengano ricevuti da Spark Streaming esattamente una sola volta, è possibile ottenere garanzie complete di tipo exactly-once. Uno dei punti di forza di Spark Streaming è il supporto della tolleranza di errore, con ripristino rapido dei nodi con errori quando si usano più nodi all'interno del cluster.

Per altre informazioni, vedere [Informazioni su Spark Streaming](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Scalabilità di un cluster

Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di [modificare il numero di nodi del cluster](hdinsight-administer-use-management-portal.md#scale-clusters). È possibile eliminare i cluster Spark senza alcuna perdita di dati, perché tutti i dati sono archiviati in Archiviazione di Azure o Data Lake Store.

Esistono dei vantaggi rispetto alle tecnologie di separazione. Ad esempio, Kafka è una tecnologia per la memorizzazione nel buffer di eventi, quindi prevede un uso molto elevato delle risorse di I/O e non necessita di molta potenza di elaborazione. In confronto, gli elaboratori di flussi come Spark Streaming, richiedono grandi quantità di risorse di calcolo e quindi macchine virtuali più potenti. Separando queste tecnologie in cluster diversi è possibile gestirne la scalabilità in modo indipendente e usare al tempo stesso in modo ottimale le macchine virtuali.

### <a name="scale-the-stream-buffering-layer"></a>Scalabilità del livello di memorizzazione nel buffer dei flussi

Le tecnologie per la memorizzazione nel buffer dei flussi Hub eventi e Kafka usano entrambe le partizioni e i consumer leggono da tali partizioni. Per la scalabilità della velocità effettiva di input è necessario aumentare il numero di partizioni e l'aggiunta di partizioni comporta una parallelismo crescente. In Hub eventi il numero di partizioni non può essere modificato dopo la distribuzione, quindi è importante iniziare tenendo conto delle dimensioni previste. Con Kafka è possibile [aggiungere partizioni](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion) anche durante l'elaborazione dei dati. Kafka offre uno strumento per riassegnare le partizioni, `kafka-reassign-partitions.sh`. HDInsight fornisce uno [strumento di ribilanciamento della replica delle partizioni](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Questo strumento di ribilanciamento chiama lo strumento `kafka-reassign-partitions.sh` in modo che ogni replica si trovi in un dominio di errore e un dominio di aggiornamento separati, rendendo Kafka in grado di riconoscere il rack e migliorando la tolleranza di errore.

### <a name="scale-the-stream-processing-layer"></a>Scalabilità del livello di elaborazione dei flussi

Sia Apache Storm che Spark Streaming supportano l'aggiunta di nodi di lavoro ai cluster, anche durante l'elaborazione dei dati.

Per sfruttare i vantaggi di nuovi nodi aggiunti tramite la scalabilità di Storm è necessario ribilanciare le eventuali topologie Storm avviate prima dell'aumento delle dimensioni del cluster. Questo ribilanciamento può essere eseguito tramite l'interfaccia utente Web di Storm o la relativa interfaccia della riga di comando. Per altre informazioni, vedere la [documentazione di Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark usa tre parametri chiave per la configurazione dell'ambiente, a seconda dei requisiti dell'applicazione: `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un *executor* è un processo avviato per un'applicazione Spark. Viene eseguito sul nodo di lavoro ed è responsabile dell'esecuzione delle attività dell'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi di lavoro e alle relative dimensioni. Questi numeri sono archiviati nel file `spark-defaults.conf` in ogni nodo head del cluster.

Questi tre parametri possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, e possono anche essere specificati per ogni singola applicazione. Per altre informazioni, vedere [Gestire le risorse del cluster Apache Spark in Azure HDInsight](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Apache Storm in HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Topologie di esempio per Apache Storm in HDInsight](storm/apache-storm-example-topology.md)
* [Introduzione a Spark in HDInsight](spark/apache-spark-overview.md)
* [Iniziare a usare Apache Kafka in HDInsight](kafka/apache-kafka-get-started.md)