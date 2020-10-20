---
title: Architetture di continuità aziendale di Azure HDInsight
description: Questo articolo illustra le diverse architetture di continuità aziendale disponibili per HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: alta disponibilità di hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: c2c5e5d0dc90f8f41882f6a63497a197cd74f0ce
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207581"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Architetture di continuità aziendale di Azure HDInsight

Questo articolo fornisce alcuni esempi di architetture di continuità aziendale che è possibile prendere in considerazione per Azure HDInsight. La tolleranza per le funzionalità ridotte durante un'emergenza è una decisione aziendale che varia da un'applicazione all'altra. Potrebbe essere accettabile che alcune applicazioni non siano disponibili o siano parzialmente disponibili con funzionalità ridotte o elaborazione posticipata per un periodo di tempo. Per altre applicazioni, eventuali funzionalità ridotte potrebbero essere inaccettabili.

> [!NOTE]
> Le architetture presentate in questo articolo non sono esaustive. Si consiglia di progettare architetture univoche dopo aver apportato obiettivi mirati alla continuità aziendale prevista, alla complessità operativa e al costo di proprietà.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive e query interattive

La [replica di hive V2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) è consigliata per la continuità aziendale nei cluster HDInsight hive e Interactive query. Le sezioni persistenti di un cluster hive autonomo che devono essere replicate sono il livello di archiviazione e il metastore Hive. I cluster hive in uno scenario multiutente con Enterprise Security Package necessitano di Azure Active Directory Domain Services e Metastore Ranger.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Architettura Interactive query e hive":::

La replica basata su eventi hive è configurata tra i cluster primari e secondari. Questo è costituito da due fasi distinte, ovvero il bootstrap e le esecuzioni incrementali:

* Il bootstrap replica l'intero warehouse di hive, incluse le informazioni metastore Hive dal database primario a quello secondario.

* Le esecuzioni incrementali vengono automatizzate nel cluster primario e gli eventi generati durante le esecuzioni incrementali vengono riprodotti nel cluster secondario. Il cluster secondario rileva gli eventi generati dal cluster primario, assicurando che il cluster secondario sia coerente con gli eventi del cluster primario dopo l'esecuzione della replica.

Il cluster secondario è necessario solo al momento della replica per eseguire la copia distribuita, `DistCp` , ma è necessario che l'archiviazione e i Metastore siano permanenti. È possibile scegliere di creare un cluster secondario con script su richiesta prima della replica, eseguire lo script di replica su di esso e quindi rimuoverlo dopo la corretta replica.

Il cluster secondario è in genere di sola lettura. È possibile fare in modo che il cluster secondario sia di lettura/scrittura, ma ciò consente di aggiungere ulteriore complessità che comporta la replica delle modifiche dal cluster secondario al cluster primario.

### <a name="hive-event-based-replication-rpo--rto"></a>Replica basata su eventi hive RPO & RTO

* RPO: la perdita di dati è limitata all'ultimo evento di replica incrementale completato dal primario al database secondario.

* RTO: tempo che intercorre tra l'errore e la ripresa di transazioni upstream e downstream con il database secondario.

### <a name="apache-hive-and-interactive-query-architectures"></a>Architetture di query interattive e Apache Hive

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Hive primario attivo con database secondario su richiesta

In una replica *primaria attiva con architettura secondaria su richiesta* , le applicazioni scrivono nell'area primaria attiva, mentre non viene effettuato il provisioning di un cluster nell'area secondaria durante le normali operazioni. Il Metastore e l'archiviazione SQL nell'area secondaria sono persistenti, mentre il cluster HDInsight viene inserito nello script e distribuito su richiesta solo prima dell'esecuzione della replica hive pianificata.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Architettura Interactive query e hive":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Hive primario attivo con standby secondario

In una replica *primaria attiva con la replica secondaria standby*, le applicazioni scrivono nell'area primaria attiva mentre un cluster secondario con scalabilità in standby viene eseguito in modalità di sola lettura durante le normali operazioni. Durante le normali operazioni, è possibile scegliere di eseguire l'offload delle operazioni di lettura specifiche dell'area nel database secondario.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Architettura Interactive query e hive":::

Per altre informazioni sulla replica di hive e sugli esempi di codice, vedere [Apache hive replica nei cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-replication)

## <a name="apache-spark"></a>Apache Spark

I carichi di lavoro Spark possono essere o meno coinvolti in un componente hive. Per abilitare i carichi di lavoro di Spark SQL per la lettura e la scrittura di dati da hive, i cluster HDInsight Spark condividono Metastore personalizzati hive da cluster hive/Interactive query nella stessa area. In questi scenari, la replica tra aree dei carichi di lavoro Spark deve accompagnare anche la replica dei Metastore hive e dell'archiviazione. Gli scenari di failover in questa sezione si applicano a entrambi:

* [Spark SQL sulle tabelle acid con il programma di installazione di hive warehouse Connector (HWC)](./interactive-query/apache-hive-warehouse-connector.md) con un cluster HDInsight Interactive query.
* Carico di lavoro SQL Spark su tabelle non ACID con un cluster Hadoop HDInsight.

Per gli scenari in cui Spark funziona in modalità autonoma, i dati curati e i jar Spark archiviati (per i processi di Livio) devono essere replicati a intervalli regolari dall'area primaria all'area secondaria usando Azure Data Factory `DistCP` .

Si consiglia di usare i sistemi di controllo della versione per archiviare notebook e librerie Spark in cui possono essere facilmente distribuiti nei cluster primari o secondari. Assicurarsi che le soluzioni basate su notebook e non notebook siano pronte per caricare i montaggi dei dati corretti nell'area di lavoro primaria o secondaria.

Se sono presenti librerie specifiche del cliente che esulano dal HDInsight fornito in modo nativo, è necessario monitorarle e caricarle periodicamente nel cluster secondario standby.  

### <a name="apache-spark-replication-rpo--rto"></a>Replica Apache Spark RPO & RTO

* RPO: la perdita di dati è limitata all'ultima replica incrementale riuscita (Spark e hive) dal database primario a quello secondario.

* RTO: tempo che intercorre tra l'errore e la ripresa di transazioni upstream e downstream con il database secondario.

### <a name="apache-spark-architectures"></a>Architetture Apache Spark

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Database primario Spark attivo con secondario su richiesta

Le applicazioni leggono e scrivono nei cluster Spark e hive nell'area primaria, mentre non viene eseguito il provisioning di cluster nell'area secondaria durante le normali operazioni. Il Metastore SQL, l'archiviazione hive e l'archiviazione Spark sono persistenti nell'area secondaria. I cluster Spark e hive vengono inclusi nello script e distribuiti su richiesta. La replica hive viene usata per replicare l'archiviazione hive e i Metastore hive mentre è `DistCP` possibile usare Azure Data Factory per copiare l'archiviazione Spark autonoma. I cluster hive devono essere distribuiti prima dell'esecuzione di ogni replica hive a causa del calcolo delle dipendenze `DistCp` .

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Architettura Interactive query e hive":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Primaria attiva Spark con standby secondario

Le applicazioni leggono e scrivono nei cluster Spark e hive nell'area primaria, mentre i cluster hive e Spark con scalabilità in standby vengono eseguiti in modalità di sola lettura nell'area secondaria durante le normali operazioni. Durante le normali operazioni, è possibile scegliere di eseguire l'offload delle operazioni di lettura hive e Spark specifiche dell'area nel database secondario.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Architettura Interactive query e hive":::

## <a name="apache-hbase"></a>Apache HBase

La replica HBase export e HBase sono modi comuni per abilitare la continuità aziendale tra i cluster HDInsight HBase.

L'esportazione HBase è un processo di replica batch che usa l'utilità di esportazione HBase per esportare le tabelle dal cluster HBase primario all'archiviazione Azure Data Lake Storage generazione 2 sottostante. È quindi possibile accedere ai dati esportati dal cluster HBase secondario e importarli in tabelle che devono essere preesistenti nel database secondario. Mentre l'esportazione di HBase offre una granularità a livello di tabella, in situazioni di aggiornamento incrementale, il motore di automazione di esportazione controlla l'intervallo di righe incrementali da includere in ogni esecuzione. Per altre informazioni, vedere [HDInsight HBase backup and Replication](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import).

La replica di HBase usa la replica quasi in tempo reale tra i cluster HBase in modo completamente automatico. La replica viene eseguita a livello di tabella. Per la replica è possibile specificare come destinazione tutte le tabelle o tabelle specifiche. La replica di HBase è alla fine coerente. Ciò significa che le modifiche recenti a una tabella nell'area primaria potrebbero non essere immediatamente disponibili per tutti i database secondari. È garantito che i database secondari diventino coerenti con il database primario. La replica HBase può essere configurata tra due o più cluster HBase HDInsight se:

* Primario e secondario si trovano nella stessa rete virtuale.
* Primary e Secondary si trovano in reti virtuali con peering diversi nella stessa area.
* Primary e Secondary si trovano in reti virtuali con peering diversi in aree diverse.

Per altre informazioni, vedere [configurare la replica di cluster Apache HBase in reti virtuali di Azure](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication).

Esistono altri modi per eseguire i backup dei cluster HBase, ad esempio [la copia della cartella HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder), la [copia di tabelle](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) e [snapshot](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots).

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>Esportazione HBase

* RPO: la perdita di dati è limitata all'ultima importazione incrementale in batch da parte del database secondario dalla replica primaria.
* RTO: tempo che intercorre tra l'errore del primario e il recupero delle operazioni di I/O sul database secondario.

#### <a name="hbase-replication"></a>Replica di HBase

* RPO: la perdita di dati è limitata all'ultima spedizione WalEdit ricevuta nel database secondario.
* RTO: tempo che intercorre tra l'errore del primario e il recupero delle operazioni di I/O sul database secondario.

### <a name="hbase-architectures"></a>Architetture HBase

La replica di HBase può essere configurata in tre modalità: leader-follower, Leader-Leader e ciclico.

#### <a name="hbase-replication--leader--follower-model"></a>Replica di HBase: modello leader – follower

In questa configurazione tra aree, la replica è unidirezionale dall'area primaria all'area secondaria. È possibile identificare tutte le tabelle o tabelle specifiche nel database primario per la replica unidirezionale. Durante le normali operazioni, il cluster secondario può essere usato per gestire le richieste di lettura nella propria area.

Il cluster secondario funziona come un cluster HBase normale che può ospitare le proprie tabelle e può gestire letture e scritture da applicazioni internazionali. Tuttavia, le Scritture nelle tabelle replicate o nelle tabelle native nel database secondario non vengono replicate nel database primario.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Architettura Interactive query e hive":::

#### <a name="hbase-replication--leader--leader-model"></a>Replica di HBase: leader-modello leader

Questa configurazione tra aree è molto simile alla configurazione unidirezionale, ad eccezione del fatto che la replica avviene in modo bidirezionale tra l'area primaria e quella secondaria. Le applicazioni possono usare entrambi i cluster in modalità lettura-scrittura e gli aggiornamenti vengono scambiati in modo asincrono tra di essi.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Architettura Interactive query e hive":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>Replica di HBase: Multiregion o ciclico

Il modello di replica multiarea/ciclico è un'estensione della replica HBase e può essere usato per creare un'architettura HBase con ridondanza globale con più applicazioni che leggono e scrivono in cluster HBase specifici dell'area. I cluster possono essere configurati in varie combinazioni di leader/leader o leader/seguito, a seconda dei requisiti aziendali.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Architettura Interactive query e hive":::

## <a name="apache-kafka"></a>Apache Kafka

Per abilitare la disponibilità tra aree, HDInsight 4,0 supporta Kafka MirrorMaker che può essere usato per gestire una replica secondaria del cluster Kafka primario in un'area diversa. MirrorMaker funge da coppia consumer-Producer di alto livello, utilizza un argomento specifico nel cluster primario e produce un argomento con lo stesso nome nel database secondario. La replica tra cluster per il ripristino di emergenza a disponibilità elevata con MirrorMaker presuppone che i producer e i consumer debbano eseguire il failover nel cluster di replica. Per altre informazioni, vedere [usare MirrorMaker per replicare Apache Kafka argomenti con Kafka in HDInsight](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring)

A seconda della durata dell'argomento all'avvio della replica, la replica dell'argomento MirrorMaker può causare offset diversi tra gli argomenti di origine e di replica. I cluster HDInsight Kafka supportano anche la replica delle partizioni degli argomenti, che è una funzionalità a disponibilità elevata a livello di singolo cluster.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Architettura Interactive query e hive":::

### <a name="apache-kafka-architectures"></a>Architetture Apache Kafka

#### <a name="kafka-replication-active--passive"></a>Replica Kafka: attiva-passiva

Active-Passive installazione consente il mirroring unidirezionale asincrono da attivo a passivo. I producer e i consumer devono essere consapevoli dell'esistenza di un cluster attivo e passivo e devono essere pronti per eseguire il failover sul passivo nel caso in cui l'attività non riesca. Di seguito sono riportati alcuni vantaggi e svantaggi del programma di installazione di Active-Passive.

Vantaggi:

* La latenza di rete tra i cluster non influisce sulle prestazioni del cluster attivo.
* Semplicità della replica unidirezionale.

Svantaggi:

* Il cluster passivo può rimanere sottoutilizzato.
* Complessità progettuale nell'incorporare la consapevolezza del failover nei Producer e nei consumer di applicazioni.
* Possibile perdita di dati durante l'errore del cluster attivo.
* Coerenza finale tra gli argomenti tra i cluster attivi e passivi.
* I failback a primary può causare l'incoerenza del messaggio negli argomenti.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Architettura Interactive query e hive":::

#### <a name="kafka-replication-active--active"></a>Replica Kafka: attivo-attivo

La configurazione di Active-Active prevede due cluster Kafka HDInsight con peering a livello di area e VNet con replica asincrona bidirezionale con MirrorMaker. In questa progettazione, i messaggi utilizzati dai consumer nel database primario vengono resi disponibili anche per gli utenti in secondario e viceversa. Di seguito sono riportati alcuni vantaggi e svantaggi del programma di installazione di Active-Active.

Vantaggi:

* A causa dello stato duplicato, i failover e i failback sono più semplici da eseguire.

Svantaggi:

* La configurazione, la gestione e il monitoraggio sono più complessi rispetto a quelli attivi e passivi.
* Il problema della replica circolare deve essere risolto.  
* La replica bidirezionale comporta costi di uscita dei dati regionali più elevati.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Architettura Interactive query e hive":::

## <a name="hdinsight-enterprise-security-package"></a>Enterprise Security Package HDInsight

Questa configurazione viene usata per abilitare la funzionalità multiutente sia nel server primario che in quello secondario, nonché [Azure ad set di repliche DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) per assicurarsi che gli utenti possano eseguire l'autenticazione in entrambi i cluster. Durante le normali operazioni, è necessario configurare i criteri Ranger nel database secondario per assicurarsi che gli utenti siano limitati a operazioni di lettura. L'architettura seguente illustra il modo in cui viene configurata una configurazione secondaria Active hive primaria-standby abilitata per ESP.

Replica Metastore Ranger:

Il Metastore Ranger viene usato per archiviare e gestire in modo permanente i criteri Ranger per il controllo dell'autorizzazione dei dati. Si consiglia di mantenere i criteri Ranger indipendenti nel database primario e secondario e di mantenere il database secondario come replica di lettura.
  
Se è necessario che i criteri Ranger siano sincronizzati tra il database primario e quello secondario, usare l' [importazione/esportazione di Ranger](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) per eseguire periodicamente il backup e importare i criteri Ranger dal database primario a quello secondario.

La replica dei criteri Ranger tra i database primari e secondari può causare la scrittura del database secondario come abilitato per la scrittura, il che può portare a scritture involontarie nel database secondario, causando incoerenze dei dati.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Architettura Interactive query e hive":::

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli elementi trattati in questo articolo, vedere:

* [Continuità aziendale di Azure HDInsight](./hdinsight-business-continuity.md)
* [Architettura della soluzione a disponibilità elevata di Azure HDInsight case study](./hdinsight-high-availability-case-study.md)
* [Cosa sono Apache Hive e HiveQL in Azure HDInsight](./hadoop/hdinsight-use-hive.md)
